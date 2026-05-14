---
title: AI Agent 智能体
created: 2026-05-13
updated: 2026-05-13
tags: [AI, Agent, 智能体, ReAct]
---

# AI Agent 智能体

Agent 是能够自主决策、调用工具、与环境交互的 AI 系统。

---

## 核心特征

| 特征 | 说明 |
|------|------|
| **自主决策** | 根据目标自主选择行动路径 |
| **工具调用** | 调用外部工具完成任务 |
| **环境交互** | 与外部系统交互获取信息 |
| **反馈循环** | ReAct 模式持续优化决策 |

---

## ReAct 模式

Agent 的核心执行循环：

```
Think（思考）→ Act（行动）→ Observe（观察）→ Think → ...
```

### 执行流程

```
用户输入 → LLM 推理 → 选择工具 → 执行工具 → 获取结果 → 继续推理 → 最终答案
```

### scratchpad 中间记忆

让 LLM 看到自己曾经做了什么，帮助规划下一步。

---

## Agent 组件

| 组件 | 说明 |
|------|------|
| **AgentExecutor** | 决策循环驱动器 |
| **Tool** | 外部工具抽象 |
| **AgentAction** | 下一步行动决策 |
| **AgentFinish** | 最终输出结果 |

---

## LangChain Agent 实现

```python
from langchain.agents import create_react_agent, AgentExecutor

agent = create_react_agent(llm=llm, tools=[tool], prompt=prompt)
executor = AgentExecutor.from_agent_and_tools(
    agent=agent,
    tools=[tool],
    verbose=True
)
result = executor.invoke({"input": "问题"})
```

---

## LangGraph 图式 Agent

支持复杂 DAG 工作流（分支、循环、并发）：

```python
from langgraph.graph import StateGraph, END

workflow = StateGraph(AgentState)
workflow.add_node("think", think_node)
workflow.add_node("act", act_node)
workflow.add_conditional_edges("act", lambda s: s["next_action"],
                               {"search": "think", "end": END})
```

---

## 学习资源

| 项目 | 链接 | 说明 |
|------|------|------|
| GenericAgent | https://github.com/lsdefine/GenericAgent | 复旦大学研究 |
| hello-agents | https://github.com/datawhalechina/hello-agents | 入门教程 |

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain Agent 架构详解
- [[summaries/ai-agent-overview]] - Agent 智能体概述
- [[concepts/langchain]] - LangChain 框架
- [[concepts/langgraph]] - LangGraph 图式工作流