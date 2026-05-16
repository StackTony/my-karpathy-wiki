---
title: Multi-Agent 四种架构流派
created: 2026-05-14
updated: 2026-05-14
tags: [AI, Multi-Agent, LangGraph, AutoGen, CrewAI]
credibility: low
source_dir: Self learn
source_files: [多agent编排-Comet-Agent架构模式.md]
---

# Multi-Agent 四种架构流派

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

从单一巨大 System Prompt 到专业化协作 Agent 的架构转变。

---

## 核心驱动力

### Long Context Window Trap

"Lost in the Middle" 现象：即使百万token窗口，中间信息检索准确率显著下降。

**解决方案**：分布式上下文管理：
- Market Analyst 处理季度报告（独立窗口）
- Technical Analyst 处理专利文档（独立窗口）
- Supervisor 综合两者摘要（高层洞察而非原始数据）

### Adversarial Collaboration

单一Agent天然顺从（"Yes Men"），为保持对话一致性而强化幻觉。

**Multi-Agent Debate 机制**：一个Agent批判另一个输出，事实准确率提升 23%。

---

## 四种架构流派

### 1. The Structuralists: Graph-Based Control

**主导框架**：LangGraph

| 特点 | 说明 |
|------|------|
| 节点 | 函数（Agent或Tool） |
| 边 | 控制流 |
| 状态 | Pydantic模型（严格类型检查） |
| Superstep | 并行分支等待所有完成后聚合 |

**适用**：金融交易、医疗流程、需审计轨迹和保证终止的场景。

### 2. The Interactionists: Event-Driven Scale

**主导框架**：Microsoft AutoGen (v0.4+)

| 特点 | 说明 |
|------|------|
| Actor Model | Agent作为独立Actor，私有状态 |
| Event Bus | 异步消息通信，非共享内存 |
| 分布式 | Agent可跨服务器部署 |

**适用**：高规模客服、分布式研究系统、动态添加/移除Agent。

### 3. The Role-Players: Hierarchical Teams

**主导框架**：CrewAI

| 特点 | 说明 |
|------|------|
| 组织隐喻 | Teams、Managers、Tasks |
| Persona Conditioning | 深度角色定义约束搜索空间 |
| 自动委托 | Manager Agent自动规划/分配/审查 |

**适用**：内容创作管道、清晰层级研究项目。

### 4. The Minimalists: Stateless Handoffs

**主导框架**：OpenAI Swarm

| 特点 | 说明 |
|------|------|
| Client-side Orchestration | LLM服务器无状态 |
| Handoff via Function Call | 返回新Agent配置而非更新状态 |
| 横向扩展 | 无共享状态瓶颈 |

**适用**：客服系统、多部门聊天机器人。

---

## Cognitive Architectures

### Planner-Executor Pattern

分离规划与执行：
- **Planner Agent**：无工具，生成DAG步骤
- **Executor Agent**：单步执行，报告结果

**收益**：降低45%推理成本（COPE测试）。

### Embodied Intelligence (Voyager)

Agent将复杂任务解决方案写成Python函数，存入技能库：
- 未来任务检索调用已学技能
- 无需从零推理

### Memory Streams and Reflection

检索评分公式 = Recency（指数衰减） + Importance（LLM评分1-10） + Relevance（向量相似度）

---

## 生产工程挑战

### Token Economics

单查询$0.50 vs 五并行Agent调用$3.00（系统提示重叠）。

### Latency: Weakest Link

四Agent 3秒完成，但Web Scraper 20秒→用户等待20秒。

**解决方案**：Deferred Execution - Supervisor等待所有分支后合成单答案。

### SRE Filter Pattern

Metrics Agent 快速检测异常 → 仅触发时启动 Logs Agent → 缩小搜索空间（GB→几行）。

---

## 相关链接

### Multi-Agent 系列关联

- [[summaries/multi-agent-production-patterns]] - 生产模式与基础设施需求（Orchestrator-Worker等四种模式）

### 其他关联

- [[summaries/langchain-architecture]] - LangChain设计原理
- [[concepts/langgraph]] - LangGraph图式工作流
- [[concepts/ai-agent]] - Agent智能体概念