---
title: Durable Execution 持久化执行
created: 2026-05-14
updated: 2026-05-14
tags: [AI, Agent, Distributed Systems, 持久化执行]
---

# Durable Execution 持久化执行

持久化执行是长任务 Agent 在生产环境可靠运行的核心保障，区别于简单的 Checkpointer 状态保存。

---

## 核心定义

**Durable Execution**：工作流一定会运行到完成，运行时自动处理故障检测、状态恢复、幂等保证。

---

## Checkpointer vs 持久化执行

| 维度 | Checkpointer | 持久化执行 |
|------|--------------|-----------|
| **承诺** | "保存了状态，接下来交给你" | "工作流一定运行到完成" |
| **故障恢复** | 需人或机制检测、手动触发 | 运行时自动心跳检测、回放恢复 |
| **重复执行** | 多 Worker 可能重复执行 | 幂等保证，不会重复 |
| **编排责任** | 你提供编排逻辑 | 系统为你处理 |

---

## 核心机制

### 自动故障检测

- Worker 心跳检测
- 故障时自动回放事件历史重建状态
- 在故障的精确步骤恢复，不重新运行已完成工作

### 幂等性保证

- 每次外部写入携带幂等键（工作流 ID + 步骤编号）
- 同键二次调用 → 返回原始结果的空操作（No-op）
- 防止重试触发重复副作用

### 人机协作支持

- "等待人工"是执行模型的一等状态
- Worker 可释放上下文、无限期等待
- 输入到达时干净恢复

---

## 代表框架

| 框架 | 类型 | 说明 |
|------|------|------|
| **Temporal** | 持久化执行 | 生产级可靠性，Signal 处理器支持无限等待 |
| **Trigger.dev** | 持久化执行 | AI 场景优化的人机工程学 |
| **Inngest** | 持久化执行 | 新兴专用平台 |
| LangGraph PostgresSaver | Checkpointer | 需自己构建编排逻辑 |

---

## 35 分钟衰减问题

AI Agent 连续运行约 35 分钟后可靠性下降（上下文窗口饱和 + 累积推理漂移）。

**解决方案**：有边界会话 + 外部状态重建 + 干净移交

---

## 生产部署清单

- ✓ 异步设计（超过 30 秒必须异步）
- ✓ 状态持久化（恢复所需的所有状态）
- ✓ 幂等写入（每次外部写入携带幂等键）
- ✓ 中断点定义（提前设计到工作流拓扑）
- ✓ 会话长度限制（分解为有边界会话）

---

## 相关链接

- [[summaries/agent-long-task-production]] - 长期任务生产失败完整分析
- [[summaries/agent-memory-langgraph]] - LangGraph Checkpointer 实现
- [[concepts/langgraph]] - LangGraph StateGraph 概念
- [[summaries/multi-agent-production-patterns]] - 生产基础设施需求