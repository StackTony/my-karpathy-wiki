---
title: LangChain 设计原理分析
created: 2026-05-13
updated: 2026-05-13
tags: [AI, LangChain, Agent, RAG, LLM]
source_dir: AI人工智能/Agent架构/LangChain
source_files: [LangChain 架构.md, LangChain 解决的核心问题.md]
---

# LangChain 设计原理分析

LangChain 是一套有系统设计、可维护、可实践、可扩展的 LLM 应用平台架构，支持运行时模块组合与执行流程组织。

---

## 一、AI Agent 开发的 6 个核心问题

| 问题层 | LangChain 对应模块 |
|--------|-------------------|
| 模型怎么解耦 | BaseLanguageModel 统一抽象层 |
| 上下文怎么组织 | PromptTemplate + Memory 系统 |
| 输出怎么约束 | OutputParser + 结构化输出 |
| 外部动作怎么接入 | Tool + AgentExecutor |
| 历史和知识怎么选择 | Retriever + VectorStore |
| 整条流程怎么编排和观测 | Runnable + LCEL + LangGraph |

---

## 二、架构总览：现代 AI 应用的基石

### 包结构分离

```
langchain-core      → 核心设计抽象（Runnable、LCEL）
langchain           → 高级组合模块
langchain-community → 社区扩展（各模型适配）
langgraph           → 图形化 Agent 工作流
langserve           → 应用部署（REST API）
langsmith           → 可观测化平台
```

### 核心抽象 Runnable + LCEL

将全部组件抽象为可执行单元（Runnable），支持统一接口：

| 方法 | 用途 | 说明 |
|------|------|------|
| `.invoke()` | 单次执行 | 同步调用 |
| `.stream()` | 流式输出 | 异步迭代器 |
| `.batch()` | 批量处理 | 并行执行 |
| `.transform()` | 输出转换 | 数据处理 |
| `.with_fallbacks()` | 备用路径 | 错误降级 |
| `.with_retry()` | 自动重试 | 指数退避 |

### 模块分层

```
Model I/O           → LLM 调用层
Prompt/Memory       → 输入构建层
Chains              → 流程编排层
Agents              → 自主决策层
Retriever & VectorStore → 知识检索层
LangGraph           → 图式工作流层
```

---

## 三、Runnable 与 LCEL 实现

### LCEL 链式组合

通过运算符重载 `|` 将多个 Runnable 组合成数据流管道：

```python
# 链式组合
chain = prompt | llm | StrOutputParser()

# 自定义 Runnable
class AddOne(Runnable[int, int]):
    def invoke(self, input: int, config=None) -> int:
        return input + 1

workflow = AddOne() | RunnableLambda(lambda x: x * x)
workflow.invoke(2)  # 输出：9
```

### RunnableSerializable

继承 RunnableSerializable 自动获得 `.batch()`、`.stream()`、`.bind()` 等方法：

```python
class AddOne(RunnableSerializable[int, int]):
    def invoke(self, input: int, config=None, **kwargs) -> int:
        return input + 1

# Retry + Fallback 联合使用
robust_add_one = add_one.with_retry(
    retry_if_exception_type=(ValueError, ZeroDivisionError),
    wait_exponential_jitter=True,
    stop_after_attempt=2
)
add_one_with_fallback = robust_add_one.with_fallbacks([
    RunnableLambda(lambda x: x + 5)  # fallback 模块
])
```

---

## 四、BaseLanguageModel 多模型适配

### 类继承层次

```
Runnable → BaseLanguageModel → BaseLLM/BaseChatModel → 具体实现
         ↓
         支持 OpenAI、Anthropic、DeepSeek、ChatGLM 等
```

### 核心方法对比

| 方法 | 用途 | 返回值 |
|------|------|--------|
| `invoke()` | 单次调用 | string |
| `generate()` | 批量生成 | LLMResult |
| `batch()` | 并行批处理 | string[] |
| `stream()` | 流式输出 | AsyncIterator |

---

## 五、PromptTemplate 模板系统

### 模板创建方式

```python
# 方式一：from_template
template = PromptTemplate.from_template("请回答：{question}")

# 方式二：显式定义
template = PromptTemplate(
    template="你好{name}, 请回答：{question}",
    input_variables=["name", "question"]
)

# ChatPromptTemplate 多角色对话
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个助手"),
    ("human", "{question}")
])
```

### LCEL 集成

```python
chain = prompt | llm | StrOutputParser()
```

---

## 六、Memory 系统设计

解决 LLM 无状态问题，保持对话上下文：

| 记忆类型 | 描述 | 适用场景 |
|----------|------|----------|
| ConversationBufferMemory | 存储所有对话历史 | 简单短对话 |
| ConversationBufferWindowMemory | 只保留最近N轮 | 长对话token控制 |
| ConversationSummaryMemory | 自动摘要对话内容 | 大量历史压缩 |
| VectorStoreMemory | 向量数据库存储 | 大规模知识检索 |
| EntityMemory | 跟踪对话中的关键实体 | 实体追踪 |

### 核心方法

```python
memory = ConversationBufferMemory()
memory.save_context({"input": "你好"}, {"output": "你好，有什么可以帮你？"})
context = memory.load_memory_variables({})
```

---

## 七、Agent 架构：决策循环与 ReAct

### Agent 基本职责

```
用户输入 → LLM 推理 → 选择工具并调用 → 接收反馈继续推理 → 判断是否输出最终答案
```

### ReAct 模式

采用"思考-行动-观察"循环：

```
Think → Act → Observe → Think → ...
```

### AgentExecutor 决策循环

```
用户输入 → LLM 推理出 Action → 执行 Tool 获取 Observation → 拼接进 scratchpad → 循环直到 Final Answer
```

### scratchpad 中间记忆

让 LLM 看到自己曾经做了什么，帮助规划下一步。

```python
from langchain_core.tools import Tool
tool = Tool(name="乘法计算工具", func=multiply_tool,
            description="输入形如 '3*5' 的字符串，返回乘积")

agent = create_react_agent(llm=llm, tools=[tool], prompt=prompt)
executor = AgentExecutor.from_agent_and_tools(
    agent=agent, tools=[tool], verbose=True, handle_parsing_errors=True
)
```

---

## 八、自定义 Tool 与插件

### 自定义 Tool

```python
class ReverseTool(BaseTool):
    name: str = "reverse"
    description: str = "反转输入字符串"

    def _run(self, query: str) -> str:
        return query[::-1]
```

### AgentPlugin 插件机制

通过 BaseCallbackHandler 实现日志追踪、缓存、限制等：

```python
class LoggingPlugin(BaseCallbackHandler):
    def on_agent_action(self, action, **kwargs):
        print(f"Agent 决策：调用工具 {action.tool}")

    def on_tool_end(self, output, **kwargs):
        print("工具返回：", output)
```

---

## 九、RAG 检索增强生成

### Retriever（检索器）

接收自然语言查询，返回 Document 列表。

### ChainType 合并策略

| 类型 | 说明 | 适用场景 |
|------|------|----------|
| `stuff` | 直接拼接所有片段 | 简单但易触发 token 限制 |
| `map_reduce` | 先对每片段分别生成再汇总 | 大量文档 |
| `refine` | 迭代用更多文档精炼答案 | 追求精确 |

### 整体流程

```python
# 1. 加载文档并切分
loader = DirectoryLoader("docs", glob="**/*.txt")
raw_docs = loader.load()
splitter = RecursiveCharacterTextSplitter(chunk_size=100, chunk_overlap=20)
texts = splitter.split_documents(raw_docs)

# 2. 嵌入并构建向量索引
embeddings = HuggingFaceEmbeddings(model_name="BAAI/bge-small-zh-v1.5")
vectorstore = FAISS.from_documents(texts, embeddings)

# 3. 构建 Chain
retriever = vectorstore.as_retriever(search_type="similarity", search_kwargs={"k": 2})
combine_docs_chain = create_stuff_documents_chain(llm, prompt)
chain = create_retrieval_chain(retriever=retriever, combine_docs_chain=combine_docs_chain)
```

---

## 十、向量数据库与 Retriever

### VectorStore vs Retriever

| 角色 | 说明 |
|------|------|
| VectorStore | 存储层（FAISS、Chroma、Weaviate） |
| Retriever | 查询接口层 |

### Retriever 类型

| 类型 | 说明 |
|------|------|
| VectorStoreRetriever | 基于向量相似度 |
| MultiQueryRetriever | 多角度查询扩展 |
| ContextualCompressionRetriever | 上下文压缩 |

```python
vectorstore = FAISS.from_documents(texts, embeddings)
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 4}
)
docs = retriever.get_relevant_documents("查询内容")
```

---

## 十一、LangGraph 图式 Agent 工作流

### 图式 vs 线性

LangChain Chain 是线性管道，LangGraph 支持复杂 DAG 结构（分支、循环、并发）。

### 核心组件

| 组件 | 说明 |
|------|------|
| StateGraph | 状态图容器 |
| Node | 执行节点（Runnable） |
| Edge | 条件边/普通边 |
| State | 共享状态对象 |

### 代码示例

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict

class AgentState(TypedDict):
    messages: list
    next_action: str

def think_node(state: AgentState) -> AgentState:
    # LLM 推理
    return {"next_action": "search"}

def act_node(state: AgentState) -> AgentState:
    # 执行工具
    return {"messages": state["messages"] + ["tool_result"]}

workflow = StateGraph(AgentState)
workflow.add_node("think", think_node)
workflow.add_node("act", act_node)
workflow.add_edge("think", "act")
workflow.add_conditional_edges("act", lambda s: s["next_action"],
                               {"search": "think", "end": END})
app = workflow.compile()
```

---

## 十二、LangGraph 持久化与长时间任务

### Checkpoint 机制

通过 checkpointer 存储执行状态，支持中断后恢复：

| 持久化方案 | 说明 |
|------------|------|
| MemorySaver | 内存持久化（调试用） |
| SqliteSaver | SQLite 数据库 |
| RedisSaver | Redis 存储 |

### 人类反馈介入

在关键节点暂停，等待人工输入后继续：

```python
from langgraph.checkpoint.sqlite import SqliteSaver

checkpointer = SqliteSaver("checkpoints.db")
app = workflow.compile(checkpointer=checkpointer)

config = {"configurable": {"thread_id": "user_123"}}
result = app.invoke(initial_state, config)

# 恢复执行
app.invoke(None, config)  # 从上次 checkpoint 继续
```

---

## 十三、LangServe 快速部署

将 Runnable 部署为 REST API 服务：

```python
from fastapi import FastAPI
from langserve import add_routes

app = FastAPI(title="LangChain Service")

# 添加 Runnable 路由
add_routes(app, chain, path="/chain", enable_playground=True)

# 启动服务
# uvicorn server:app --host 0.0.0.0 --port 8000
```

### 自动特性

- 支持 invoke/stream/batch 等所有 Runnable 方法
- 自动生成 OpenAPI 文档
- 支持异步调用

---

## 十四、精简版 LangChain MVP

从零开发小框架，理解核心设计原理：

```python
# MVP Runnable 抽象
class Runnable:
    def invoke(self, input) -> Any:
        raise NotImplementedError

    def __or__(self, other):
        return RunnableSequence([self, other])

class RunnableSequence(Runnable):
    def invoke(self, input):
        for runnable in self.steps:
            input = runnable.invoke(input)
        return input

# 验证
chain = PromptTemplate("你好{name}") | MockLLM() | OutputParser()
result = chain.invoke({"name": "用户"})
```

---

## 整体架构演进脉络

| 序号 | 主题 | 核心抽象 | 层级 |
|------|------|----------|------|
| ¹ | 架构总览 | Runnable + LCEL | 概览层 |
| ² | Runnable 实现 | RunnableSequence | 核心层 |
| ³ | 自定义 Runnable | RunnableSerializable | 核心层 |
| ⁴ | 模型接口 | BaseLanguageModel | 模型层 |
| ⁵ | 模板系统 | PromptTemplate | 输入层 |
| ⁶ | Memory系统 | ConversationMemory | 记忆层 |
| ⁷ | Agent决策 | AgentExecutor + ReAct | Agent层 |
| ⁸ | 工具扩展 | BaseTool + AgentAction | Agent层 |
| ⁹ | RAG实现 | Retriever + ChainType | 检索层 |
| ¹⁰ | 向量检索 | VectorStore + Retriever | 检索层 |
| ¹¹ | LangGraph图式 | StateGraph + Node | 图式层 |
| ¹² | LangGraph持久化 | Checkpointer | 图式层 |
| ¹³ | 服务部署 | LangServe + FastAPI | 部署层 |
| ¹⁴ | 原型复刻 | MVP Runnable | 实践层 |

---

## 相关链接

- [[concepts/langchain]] - LangChain 核心概念
- [[concepts/ai-agent]] - Agent 智能体概念
- [[concepts/rag]] - RAG 检索增强生成
- [[concepts/lcel]] - LCEL 表达式语言