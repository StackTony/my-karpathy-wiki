---
title: LangGraph Agent 记忆系统深度解析
created: 2026-05-14
updated: 2026-05-14
tags: [AI, Agent, LangGraph, Memory, 记忆系统]
credibility: low
source_dir: Self learn
source_files: [Agent记忆-LangGraph官方-Memory架构.md, Agent记忆-火山引擎-长短期记忆实战.md]
---

# LangGraph Agent 记忆系统深度解析

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

Agent 记忆是实现持续、连贯和个性化交互的核心基石，让 Agent 从"一次性对话工具"转变为真正的"智能体"。

---

## 一、记忆类型与核心概念

### 短期记忆（Short-term Memory）

| 特性 | 说明 |
|------|------|
| **范围** | 单个对话线程内 |
| **持久化** | Checkpointer 每步保存状态 |
| **召回** | 线程内任何时刻可回忆 |
| **实现** | InMemorySaver / PostgresSaver |

**核心机制**：
- 状态作为图的一部分管理
- 通过 thread_id 区分不同对话
- 每个 super-step 自动保存 checkpoint

### 长期记忆（Long-term Memory）

| 特性 | 说明 |
|------|------|
| **范围** | 跨对话线程共享 |
| **持久化** | Store（键值数据库） |
| **召回** | 任何时间、任何线程 |
| **组织** | Namespace（分层）+ Key（唯一标识） |

**核心机制**：
- 存储为 JSON 文档
- Namespace 类似文件夹，支持层级组织（如 `("memories", user_id)`）
- 支持语义搜索（需配置 embed）

### 对比总结

| 维度 | 短期记忆 | 长期记忆 |
|------|----------|----------|
| 持久层 | Checkpointer | Store |
| 线程关系 | 线程内 | 跨线程 |
| 更新时机 | 自动（每步） | 显式（手动 put） |
| 检索方式 | thread_id | namespace + key / search |
| 适用场景 | 对话历史、上传文件 | 用户偏好、知识积累 |

---

## 二、短期记忆管理

### InMemorySaver（开发调试）

```python
from langgraph.checkpoint.memory import InMemorySaver
from langgraph.prebuilt import create_react_agent

checkpointer = InMemorySaver()
agent = create_react_agent(model=model, tools=[], checkpointer=checkpointer)

config = {"configurable": {"thread_id": "1"}}
response = agent.invoke({"messages": [{"role": "user", "content": "你好"}]}, config)
```

**注意**：程序终止后内存释放，记忆消失。

### PostgresSaver（生产持久化）

```python
from langgraph.checkpoint.postgres import PostgresSaver

DB_URI = "postgresql://postgres:postgres@localhost:5432/postgres"
with PostgresSaver.from_conn_string(DB_URI) as checkpointer:
    checkpointer.setup()  # 第一次调用必须 setup
    graph = builder.compile(checkpointer=checkpointer)
```

**数据库表结构**：
- `checkpoints`：状态快照（thread_id, checkpoint_id, checkpoint_ns）
- `checkpoint_writes`：消息内容
- `checkpoint_blobs`：二进制数据
- `checkpoint_channels`：通道映射

### 管理长对话历史

**问题**：长对话 → LLM 上下文窗口限制 → 成本高、性能差

**解决方案**：

| 方法 | 说明 | 代码示例 |
|------|------|----------|
| **删除旧消息** | 类似 LRU 缓存 | `RemoveMessage({ id: m.id })` |
| **总结对话** | 生成摘要替代原始消息 | `summarizeConversation` 节点 |
| **Token 计数截断** | trimMessages 工具 | `maxTokens: 45, strategy: "last"` |

```python
from langchain_core.messages import trim_messages

trimMessages(messages, {
    strategy: "last",
    tokenCounter: new ChatOpenAI({ modelName: "gpt-4" }),
    maxTokens: 45,
    startOn: "human",
    endOn: ["human", "tool"],
    includeSystem: true,
})
```

---

## 三、长期记忆实现

### InMemoryStore（开发调试）

```python
from langgraph.store.memory import InMemoryStore

store = InMemoryStore()
store.put(("users",), "user_123", {"name": "ada", "language": "中文"})
user_info = store.get(("users",), "user_123")
```

### PostgresStore（生产持久化）

```python
from langgraph.store.postgres import PostgresStore
from langgraph.store.base import BaseStore

with PostgresStore.from_conn_string(DB_URI) as store:
    store.setup()
    
    # 在节点中访问 store
    def call_model(state: MessagesState, config: RunnableConfig, *, store: BaseStore):
        user_id = config["configurable"]["user_id"]
        namespace = ("memories", user_id)
        
        # 搜索记忆
        memories = store.search(namespace, query=state["messages"][-1].content)
        
        # 写入记忆
        if "记住" in last_message.content.lower():
            store.put(namespace, str(uuid.uuid4()), {"data": memory})
```

### 记忆写入时机

| 策略 | 说明 | 优缺点 |
|------|------|--------|
| **Hot Path** | 主路径实时写入 | ✓ 实时；✗ 增加延迟、复杂度 |
| **Background** | 后台异步写入 | ✓ 无延迟、逻辑分离；✗ 确定触发时机 |

---

## 四、记忆类型分类

### 三种记忆类型

| 类型 | 说明 | 存储方式 | 应用场景 |
|------|------|----------|----------|
| **Semantic（语义）** | 事实性知识 | Profile / Collection | 用户偏好、个人信息 |
| **Episodic（情景）** | 事件经历 | Few-shot 示例 | 过去对话、完成任务 |
| **Procedural（程序）** | 技能规则 | System Prompt | 任务指令、工作流 |

### 记忆表示方式

| 方式 | 说明 | 示例 |
|------|------|------|
| **更新自身指令** | 元提示精炼 System Prompt | `"agent_instructions"` namespace |
| **Few-shot 示例** | 输入-输出配对存储 | LangSmith 数据集 + BM25 检索 |

---

## 五、子图与工具中的记忆

### 子图记忆继承

| 模式 | 说明 | 配置 |
|------|------|------|
| **继承** | 子图继承父图 checkpointer | 默认 |
| **隔离** | 子图独立记忆空间 | `checkpointer=True` |

### 工具中读取状态

```python
from langgraph.prebuilt import InjectedState

def get_user_info(state: Annotated[CustomState, InjectedState]) -> str:
    user_id = state["user_id"]
    return "ada" if user_id == "user_ada" else "Unknown"
```

### 工具中写入状态

```python
from langgraph.types import Command
from langchain_core.tools import InjectedToolCallId

def update_user_info(tool_call_id: Annotated[str, InjectedToolCallId]) -> Command:
    return Command(update={
        "user_name": "ada",
        "messages": [ToolMessage("成功", tool_call_id=tool_call_id)]
    })
```

---

## 六、与已有知识关联

### LangGraph 架构关联

- [[summaries/langchain-architecture]] - LangGraph 章节（Checkpoint 持久化）
- [[concepts/langgraph]] - StateGraph/DAG/Checkpoint 概念

### Agent 架构关联

- [[summaries/ai-agent-overview]] - Agent 基础概念
- [[summaries/multi-agent-architecture-patterns]] - 多 Agent 记忆隔离（Structuralists）

---

## 相关链接

- [[concepts/agent-memory]] - Agent 记忆核心概念
- [[summaries/langchain-architecture]] - LangChain 记忆系统设计
- [[summaries/agent-long-task-production]] - 长期任务状态恢复