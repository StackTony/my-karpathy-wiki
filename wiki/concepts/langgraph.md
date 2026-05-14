---
title: LangGraph 图式 Agent 工作流
created: 2026-05-13
updated: 2026-05-13
tags: [AI, LangChain, LangGraph, Agent, DAG]
---

# LangGraph 图式 Agent 工作流

LangGraph 是 LangChain 的图式扩展，支持有状态、多步驱动、分支循环的 Agent 工作流。

---

## 图式 vs 线性

| 对比 | LangChain Chain | LangGraph |
|------|-----------------|-----------|
| **结构** | 线性管道 | DAG 图 |
| **分支** | ❌ 不支持 | ✓ 条件边 |
| **循环** | ❌ 不支持 | ✓ 循环边 |
| **并发** | ❌ 不支持 | ✓ 并行节点 |

---

## 核心组件

| 组件 | 说明 |
|------|------|
| **StateGraph** | 状态图容器 |
| **Node** | 执行节点（Runnable） |
| **Edge** | 普通边 / 条件边 |
| **State** | 共享状态对象（TypedDict） |

---

## 状态管理

通过 TypedDict 定义 State，节点间通过 State 传递数据：

```python
from typing import TypedDict

class AgentState(TypedDict):
    messages: list
    next_action: str
```

---

## 代码示例

```python
from langgraph.graph import StateGraph, END

workflow = StateGraph(AgentState)
workflow.add_node("think", think_node)
workflow.add_node("act", act_node)
workflow.add_edge("think", "act")
workflow.add_conditional_edges("act", lambda s: s["next_action"],
                               {"search": "think", "end": END})
app = workflow.compile()
```

---

## 持久化 Checkpoint

支持中断恢复：

| 持久化方案 | 说明 |
|------------|------|
| MemorySaver | 内存（调试用） |
| SqliteSaver | SQLite 数据库 |
| RedisSaver | Redis 存储 |

```python
from langgraph.checkpoint.sqlite import SqliteSaver

checkpointer = SqliteSaver("checkpoints.db")
app = workflow.compile(checkpointer=checkpointer)

# 恢复执行
app.invoke(None, {"thread_id": "user_123"})
```

---

## 相关链接

- [[summaries/langchain-architecture]] - LangGraph 章节
- [[concepts/langchain]] - LangChain 框架
- [[concepts/ai-agent]] - Agent 智能体