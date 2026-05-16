---
title: Plan-and-Execute 规划执行范式
created: 2026-05-14
updated: 2026-05-14
tags: [AI, Agent, Planning, Plan-and-Execute]
---

# Plan-and-Execute 规划执行范式

Plan-and-Execute 是一种分离规划与执行的 Agent 架构，解决了 ReAct 模式的成本和规划深度问题。

---

## 核心定义

**Plan-and-Execute**：先制定完整的多步计划，再逐步执行，执行完成后判断是否需要重新规划。

---

## 与 ReAct 对比

| 维度 | ReAct | Plan-and-Execute |
|------|-------|------------------|
| **规划方式** | 单步思考 | 全局多步计划 |
| **LLM 调用** | 每步调用 1 次 | 仅规划 + 最终响应 |
| **成本** | 高 | 低（可分层模型） |
| **规划深度** | 局部优化 | 全局优化 |
| **适用场景** | 简单任务 | 多步复杂任务 |

---

## 核心组件

| 组件 | 职责 | 模型选择 |
|------|------|----------|
| **Planner** | 生成多步计划 | 大模型（GPT-4/Claude） |
| **Executor** | 执行单步任务 | 小模型 / 工具直接调用 |
| **Re-planner** | 判断完成或后续计划 | 大模型 |

---

## 三种架构演进

| 架构 | 特点 | 关键创新 |
|------|------|----------|
| **Plan-and-Execute** | 串行执行计划列表 | 分离规划与执行 |
| **ReWOO** | 变量引用 + 无需每步规划 | `#E1` 引用前序输出 |
| **LLMCompiler** | DAG + 并行 + 流式 | 依赖满足即执行 |

---

## 三大优势

1. **更快执行**：大模型不参与每步，子任务可用轻量模型
2. **成本节省**：大模型仅用于规划和最终响应
3. **更好效果**：强制 Planner "想透" 整个任务

---

## LangGraph 实现

```python
workflow = StateGraph(PlanExecute)
workflow.add_node("planner", plan_step)
workflow.add_node("agent", execute_step)
workflow.add_node("replan", replan_step)
workflow.add_conditional_edges("replan", should_end, {"end": END, "continue": "planner"})
```

---

## 相关链接

- [[summaries/plan-and-execute-agent]] - Plan-and-Execute 完整实现与 ReWOO/LLMCompiler
- [[summaries/langchain-architecture]] - Agent 架构章节（ReAct vs Plan-Execute）
- [[concepts/ai-agent]] - Agent 智能体概念
- [[concepts/langgraph]] - LangGraph StateGraph 概念