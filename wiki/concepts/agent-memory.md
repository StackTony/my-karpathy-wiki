---
title: Agent Memory 记忆系统核心概念
created: 2026-05-14
updated: 2026-05-14
tags: [AI, Agent, Memory, 记忆]
---

# Agent Memory 记忆系统核心概念

Agent 记忆是实现持续、连贯和个性化交互的核心基石，让 Agent 从"一次性对话工具"转变为真正的"智能体"。

---

## 核心定义

**Memory**：赋予 Agent 记忆能力的技术和架构，能够记住过去的交互、学到的知识、执行过的任务及未来的计划。

---

## 记忆类型

### 短期记忆（Short-term Memory）

| 特性 | 说明 |
|------|------|
| **范围** | 单个对话线程内 |
| **持久化** | Checkpointer 自动保存状态 |
| **召回** | 线程内任何时刻可回忆 |
| **用途** | 对话历史、上传文件、检索文档 |

### 长期记忆（Long-term Memory）

| 特性 | 说明 |
|------|------|
| **范围** | 跨对话线程共享 |
| **持久化** | Store（键值数据库）显式写入 |
| **召回** | 任何时间、任何线程 |
| **用途** | 用户偏好、知识积累、任务历史 |

---

## 记忆内容类型

| 类型 | 说明 | 存储方式 | 应用场景 |
|------|------|----------|----------|
| **Semantic（语义）** | 事实性知识 | Profile / Collection | 用户偏好、个人信息 |
| **Episodic（情景）** | 事件经历 | Few-shot 示例 | 过去对话、完成任务 |
| **Procedural（程序）** | 技能规则 | System Prompt | 任务指令、工作流 |

---

## LangGraph 实现机制

| 组件 | 短期记忆 | 长期记忆 |
|------|----------|----------|
| **持久层** | Checkpointer | Store |
| **更新时机** | 自动（每步） | 显式（手动 put） |
| **检索方式** | thread_id | namespace + key / search |
| **数据库支持** | PostgresSaver / RedisSaver | PostgresStore / InMemoryStore |

---

## 关键设计决策

| 问题 | 选项 | 说明 |
|------|------|------|
| **写入时机** | Hot Path / Background | 实时 vs 异步批量 |
| **管理方式** | Profile / Collection | 单文档持续更新 vs 多文档集合 |
| **表示方式** | 指令更新 / Few-shot | 系统提示修改 vs 示例存储 |

---

## 相关链接

- [[summaries/agent-memory-langgraph]] - LangGraph 记忆系统完整实现
- [[summaries/langchain-architecture]] - Memory 系统设计章节
- [[concepts/ai-agent]] - Agent 智能体概念
- [[concepts/rag]] - RAG 与记忆的关系