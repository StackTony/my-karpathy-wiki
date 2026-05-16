---
title: Plan-and-Execute Agent 范式详解
created: 2026-05-14
updated: 2026-05-14
tags: [AI, Agent, LangGraph, Plan-and-Execute, ReWOO, LLMCompiler]
credibility: low
source_dir: Self learn
source_files: [PlanExecute-LangChain-PlanningAgents.md]
---

# Plan-and-Execute Agent 范式详解

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

Plan-and-Execute 范式通过分离规划与执行，解决了 ReAct Agent 的两大痛点：每步调用 LLM 成本高、仅规划单步导致轨迹不优。

---

## 一、ReAct Agent 的局限性

### ReAct 循环

```
Thought → Act → Observe → Thought → ...
```

**优点**：Chain-of-thought 提升单步决策质量

**缺点**：
1. 每次工具调用都需要 LLM 调用（成本高）
2. LLM 仅规划 1 个子问题（不强制全局推理）

### 复合概率的残酷数学

| 步骤数 | 每步 95% 成功率 | 每步 99% 成功率 |
|--------|----------------|----------------|
| 10 步 | 60% 最终成功 | 90% |
| 20 步 | 36% | 82% |
| 30 步 | 21% | 74% |

**结论**：长任务必须设计为有界工作流，而非开放式规划。

---

## 二、Plan-and-Execute 架构

### 核心组件

| 组件 | 职责 | 模型选择 |
|------|------|----------|
| **Planner** | 生成多步计划 | 大模型（GPT-4/Claude） |
| **Executor** | 执行单步任务 | 小模型 / 工具直接调用 |
| **Re-planner** | 判断完成或生成后续计划 | 大模型 |

### 工作流程

```
用户输入 → Planner 生成计划（Step 1, 2, 3...）
           ↓
        Executor 执行 Step 1
           ↓
        Re-planner 判断：完成? / 继续?
           ↓
        循环直到 Final Answer
```

### LangGraph 实现

```python
from langgraph.graph import StateGraph, START, END

workflow = StateGraph(PlanExecute)
workflow.add_node("planner", plan_step)
workflow.add_node("agent", execute_step)
workflow.add_node("replan", replan_step)

workflow.add_edge(START, "planner")
workflow.add_edge("planner", "agent")
workflow.add_edge("agent", "replan")
workflow.add_conditional_edges("replan", should_end, {"end": END, "continue": "planner"})
```

### 三大优势

| 优势 | 说明 |
|------|------|
| **更快执行** | 大模型不参与每步，子任务可用轻量模型或直接工具 |
| **成本节省** | 大模型仅用于规划和最终响应 |
| **更好效果** | 强制 Planner "想透" 整个任务，分步专注执行 |

---

## 三、进阶架构

### ReWOO（Reasoning WithOut Observations）

**核心创新**：支持变量引用，无需每步重新规划。

**Planner 输出格式**：

```
Plan: 需要知道今年超级碗参赛队伍
E1: Search[Who is competing in the superbowl?]
Plan: 需要知道每个队伍的四分卫
E2: LLM[Quarterback for #E1[0]]
E3: LLM[Quarterback for #E1[1]]
Plan: 需要查找四分卫数据
E4: Search[Stats for #E2]
E5: Search[Stats for #E3]
```

**组件**：
- Planner：生成 Plan + E# 交错计划
- Worker：循环执行任务，赋值变量
- Solver：整合所有输出为最终答案

**优势**：每个任务只携带必要上下文，避免信息过载。

### LLMCompiler

**核心创新**：DAG 任务流 + 并行执行 + 流式输出。

**三大组件**：

| 组件 | 职责 |
|------|------|
| **Planner** | 流式输出 DAG 任务（工具、参数、依赖） |
| **Task Fetching Unit** | 依赖满足即调度执行，并行最大化 |
| **Joiner** | 判断完成或重新规划 |

**关键优化**：
- Planner 输出流式解析，立即获取任务参数和依赖
- 任务依赖满足即执行，无需等待完整计划
- 支持变量引用：`search("${1}")` 使用任务 1 输出

**性能提升**：论文声称 3.6x 加速。

---

## 四、三种架构对比

| 维度 | Plan-and-Execute | ReWOO | LLMCompiler |
|------|------------------|-------|--------------|
| **计划格式** | 步骤列表 | Plan + E# + 变量 | DAG 任务流 |
| **执行模式** | 串行 | 串行 + 变量传递 | 并行 + 流式 |
| **LLM 调用** | 每步 1 次 | 仅首次 + Solver | 仅 Planner + Joiner |
| **速度** | 中 | 中 | 快（并行） |
| **复杂度** | 低 | 中 | 高 |

---

## 五、生产实践建议

### 模型分层

- **Planner**：大模型（GPT-4/Claude-3.5）- 需强推理
- **Executor**：领域小模型（GPT-3.5/DeepSeek）- 任务单一
- **Final Response**：大模型 - 整合总结

### 计划粒度

- 太粗：子任务仍复杂，执行困难
- 太细：计划步骤过多，管理成本高
- **平衡点**：每个子任务可独立完成，无需再分解

### Re-plan 条件

- 任务执行失败 → 重新规划替代方案
- 计划未达到预期效果 → 生成后续计划
- 依赖信息缺失 → 补充信息获取步骤

---

## 六、与已有知识关联

### LangChain 架构关联

- [[summaries/langchain-architecture]] - Agent 章节（ReAct 循环）
- [[concepts/ai-agent]] - Agent 概念（自主决策）

### LangGraph 工作流关联

- [[concepts/langgraph]] - StateGraph/DAG 概念
- [[summaries/agent-memory-langgraph]] - Checkpoint 支持中断恢复

---

## 相关链接

- [[concepts/plan-and-execute]] - Plan-Execute 核心概念
- [[summaries/agent-long-task-production]] - 长期任务调度与恢复
- [[summaries/multi-agent-architecture-patterns]] - Planner-Executor Pattern（多 Agent）