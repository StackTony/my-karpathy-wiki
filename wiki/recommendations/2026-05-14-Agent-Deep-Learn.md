---
title: Agent 开发技术栈深度学习推荐
created: 2026-05-14
updated: 2026-05-14
tags: [AI, Agent, LangChain, LangGraph, RAG, Memory, 推荐]
source: 1-Day learn.md 用户指定主题
---

# Agent 开发技术栈深度学习推荐

根据 [[recommendations/1-Day learn]] 指定主题，针对 **Agent开发技术栈** 方向进行深度推荐。

---

## 已有知识总结

知识库已覆盖以下 Agent 相关内容：

| 主题               | Wiki 页面                                         | 说明                                                             |
| ---------------- | ----------------------------------------------- | -------------------------------------------------------------- |
| Agent 基础概念       | [[summaries/ai-agent-overview]]                 | 自主决策、工具调用、ReAct 循环                                             |
| LangChain 架构     | [[summaries/langchain-architecture]]            | Runnable/LCEL/Agent/RAG/LangGraph 十四篇章                         |
| LangGraph 工作流    | [[concepts/langgraph]]                          | StateGraph/DAG/Checkpoint 持久化                                  |
| Multi-Agent 架构   | [[summaries/multi-agent-architecture-patterns]] | 四种架构流派：Structuralists/Interactionists/Role-Players/Minimalists |
| Multi-Agent 生产模式 | [[summaries/multi-agent-production-patterns]]   | Orchestrator-Worker/Router/Hierarchical/Critic-Refiner 四种模式    |
| Prompt 工程        | [[summaries/prompt-engineering]]                | 角色定义、任务拆解、模板系统                                                 |
| RAG 基础           | [[concepts/rag]]                                | 检索+生成，VectorStore/Retriever                                    |

**知识缺口**：Agent 记忆系统深入实践、RAG 生产级优化、Plan-and-Execute 范式、长期任务调度恢复。

---

## 深度学习推荐博客

### 一、Agent 记忆系统（长期记忆 & Human-Readable Memory）

| 博客 | 链接 | 类别 | 重点内容 |
|------|------|------|----------|
| LangGraph 官方 Memory 文档 | [LangChain 教程](https://github.langchain.ac.cn/langgraph/concepts/memory/) | 原理介绍 | 短期记忆（Checkpointer）vs 长期记忆（Store），namespace 层级组织，跨线程检索 |
| Agent Memory Patterns 完整指南 | [Analytics Vidhya](https://www.analyticsvidhya.com/blog/2026/05/ai-agent-memory-patterns/) | 技术分析 | Short-term/Episodic/Semantic/Procedural 四种记忆类型，LangGraph Colab 实践 Demo |
| LangGraph 长短期记忆实战 | [火山引擎 ADG](https://adg.csdn.net/697089bf437a6b40336a9ab6.html) | 技术分析 | PostgreSQL 持久化、Embedding 语义搜索、MCP 协议集成 Multi-Agent |
| MongoDB + LangGraph 长期记忆 | [MongoDB Blog](https://www.mongodb.com/company/blog/product-release-announcements/powering-long-term-memory-for-agents-langgraph) | 原理介绍 | Store 接口与 Checkpointer 配合，生产级记忆管理架构 |
| HackMD Agent Memory 详解 | [HackMD](https://hackmd.io/@YungHuiHsu/S1f1cyOnke) | 技术分析 | Semantic/Episodic/Procedural 记忆对应，Hot path vs Background 写入策略 |
| Redis + LangGraph Agent 记忆 | [Redis 教程](https://redis.ac.cn/learn/what-is-agent-memory-example-using-lang-graph-and-redis) | 技术分析 | MemoryStrategy（TOOLS/MANUAL），ULID 标识，Episodic/Semantic 类型分离 |

**核心知识点**：
- 短期记忆：线程级 Checkpointer 持久化，trim_messages 控制 token
- 长期记忆：Store 接口 + namespace 层级 + 向量检索
- 记忆类型：Semantic（事实）、Episodic（经历）、Procedural（技能）
- 写入策略：Hot path（即时）vs Background（异步批量）

---

### 二、RAG 生产实践（索引/召回/重排/缓存/增量更新/评测）

| 博客              | 链接                                                              | 类别   | 重点内容                                                              |
| --------------- | --------------------------------------------------------------- | ---- | ----------------------------------------------------------------- |
| 2026 RAG 全景万字长文 | [腾讯云](https://cloud.tencent.com/developer/article/2654878)      | 原理介绍 | 混合检索（向量+BM25）、RRF 融合、Reranking、Self-RAG、GraphRAG、数据飞轮             |
| RAG 系统测试实战      | [腾讯云](https://cloud.tencent.com/developer/article/2648304)      | 技术分析 | 黄金测试集构建、双轨验证流水线、混沌工程注入、OpenTelemetry 链路追踪                         |
| RAG 评测完整指南      | [博客园](https://www.cnblogs.com/aifrontiers/p/19293942)           | 技术分析 | 检索评估（Recall@K/MRR）、生成评估（LLM-as-Judge）、评测数据集构建                     |
| B站大规模召回系统工程     | [掘金](https://juejin.cn/post/7456991860130660403)                | 技术分析 | Faiss IVF/HNSW 索引、增量更新（Kafka+delta）、分布式索引构建                       |
| 企业级 RAG 架构指南    | [阿里云](https://developer.aliyun.com/article/1628030)             | 原理介绍 | Multi-Query Retriever、Ensemble 检索、Long-Context Reorder、缓存策略       |
| 阿里云 RAG 效果优化    | [阿里云](https://help.aliyun.com/zh/model-studio/rag-optimization) | 原理介绍 | 评测集创建（事实/比较/教程/分析）、诊断改进流程                                         |
| RAG 技术范式演进      | [智源社区](https://hub.baai.ac.cn/view/43613)                       | 原理介绍 | Naive RAG → Advanced RAG → Modular RAG → Agentic RAG，LightRAG 图结构 |
| 10万文档 RAG 架构实战  | [腾讯云](https://cloud.tencent.com/developer/article/2595188)      | 技术分析 | HybridRetriever、语义缓存（64% 命中率）、元数据索引过滤                             |

**核心知识点**：
- **混合检索**：向量（语义）+ BM25（关键词），RRF 融合算法
- **重排序**：Bi-Encoder 快速召回 → Cross-Encoder 精确重排
- **增量更新**：Kafka 流式接入 → rt 索引 → delta 索引 → base 紂引
- **评测体系**：检索（Recall@K/NDCG）、生成（事实性/完整性/可读性）
- **可观测性**：OpenTelemetry 链路追踪，每环节耗时/召回ID/rerank分布
- **缓存策略**：语义缓存减少 API 调用，Prompt-Response 数据库支持微调

---

### 三、Plan-and-Execute Agent 范式

| 博客 | 链接 | 类别 | 重点内容 |
|------|------|------|----------|
| LangGraph Plan-and-Execute 官方教程 | [LangChain 教程](https://github.langchain.ac.cn/langgraph/tutorials/plan-and-execute/plan-and-execute/) | 原理介绍 | Planner 拆解任务 → Executor 逐步执行 → Replan 循环 |
| LangChain Plan-and-Execute Agents 博客 | [LangChain Blog](https://www.langchain.com/blog/planning-agents) | 原理介绍 | 对比 ReAct，优势：明确长期规划、成本节省（小模型执行） |
| LangGraph Forum 实战讨论 | [LangChain Forum](https://forum.langchain.com/t/what-is-the-best-way-to-implement-plan-and-execute-with-langchain-1-0-and-langgraph/2205) | 技术分析 | 静态路由 → 动态 Planner + SubAgent，Command 状态更新 |
| DeepSeek Plan-and-Execute 实战 | [博客园](https://www.cnblogs.com/yclh/p/19867248) | 技术分析 | Planner + Executor + Memory 协同，多轮对话历史注入 |
| LangGraph 多智能体工作流 | [LangChain Blog](https://www.langchain.com/blog/langgraph-multi-agent-workflows) | 原理介绍 | Supervisor + SubAgents，Hierarchical Agent Teams |
| Kaggle Plan-to-Execution Notebook | [Kaggle](https://www.kaggle.com/code/ksmooi/langgraph-from-planning-to-execution) | 技术分析 | State 定义、Planning/RePlanning 逻辑、Graph 构建 |

**核心知识点**：
- **架构分离**：Planner（大模型规划）+ Executor（小模型/工具执行）
- **工作流**：Plan → Execute → Replan → 循环直到 Final Answer
- **优势**：降低推理成本（45%）、明确长期规划、支持动态子任务
- **LangGraph 实现**：StateGraph + planner/agent/replan 三节点

---

### 四、Agent 任务队列与长期任务恢复

| 博客 | 链接 | 类别 | 重点内容 |
|------|------|------|----------|
| 长任务 Agent 生产失败分析 | [TianPan.co](https://tianpan.co/zh/blog/2025-10-28-async-ai-agents-long-horizon-tasks) | 技术分析 | 30秒同步边界、Checkpointer vs 持久化执行、幂等性、Temporal 框架 |
| Agent Framework 报告 | [GitHub](https://github.com/eulerai-au/blog/blob/main/agent_framework/agent_framework_report_zh.md) | 技术分析 | 各框架 Checkpointer/跨线程持久化对比，异步并发支持 |
| LangChain 内存管理上下文优化 | [腾讯云](https://cloud.tencent.com/developer/article/2557090) | 技术分析 | trim_messages、子代理上下文隔离、Supervisor 协调架构 |

**核心知识点**：
- **30秒同步边界**：超过30秒必须异步，返回 Task ID + 轮询/Webhook
- **Checkpointer vs 持久化执行**：
  - Checkpointer：保存状态，需手动恢复编排
  - 持久化执行（Temporal）：自动故障检测、回放恢复、幂等保证
- **幂等性**：外部写入必须是幂等的，避免重试重复执行
- **分布式部署**：Ray + LangGraph、Celery、K8s Pod autoscaling

---

## 推荐学习顺序

### 第一阶段：记忆系统深入（核心缺失）

1. **LangGraph 官方 Memory 文档** → 理解短期/长期记忆架构
2. **Agent Memory Patterns 完整指南** → 四种记忆类型 + Colab 实践
3. **LangGraph 短期记忆实战** → PostgreSQL + Embedding 生产案例

### 第二阶段：RAG 生产优化

1. **2026 RAG 全景** → 混合检索 + 重排 + 数据飞轮全景
2. **RAG 评测完整指南** → 检索/生成分离评测方法
3. **B站大规模召回系统工程** → 索引构建 + 增量更新架构

### 第三阶段：Plan-and-Execute 范式

1. **LangGraph Plan-and-Execute 官方教程** → 基础架构实现
2. **LangChain Plan-and-Execute Agents 博客** → 与 ReAct 对比优势
3. **DeepSeek Plan-and-Execute 实战** → 完整代码示例

### 第四阶段：长期任务调度

1. **长任务 Agent 生产失败分析** → 理解生产陷阱和解决方案
2. **Agent Framework 报告** → 框架对比选择

---

## 建议新增 Wiki 页面

| 页面 | 来源 | 说明 |
|------|------|------|
| `concepts/agent-memory` | Memory 文档整合 | 短期/长期记忆架构，四种类型 |
| `summaries/rag-production-practice` | RAG 全景 + 评测 | 生产级索引/召回/重排/缓存/评测 |
| `concepts/plan-and-execute` | LangGraph 教程 | Plan-Execute 范式架构 |
| `summaries/agent-long-task` | TianPan 博客 | 长期任务调度、持久化执行 |

---

## 相关链接

- [[recommendations/1-Day learn]] - 用户指定学习主题
- [[summaries/ai-agent-overview]] - 已有 Agent 基础概念
- [[summaries/langchain-architecture]] - 已有 LangChain 架构
- [[summaries/multi-agent-architecture-patterns]] - 已有 Multi-Agent 架构
- [[concepts/rag]] - 已有 RAG 基础概念
- [[concepts/langgraph]] - 已有 LangGraph 概念