---
title: LangChain
created: 2026-05-13
updated: 2026-05-13
tags: [AI, LangChain, LLM, Agent]
---

# LangChain

LangChain 是一套 LLM 应用开发框架，解决 AI Agent 开发的 6 个核心问题。

---

## 定位

| 角色 | 说明 |
|------|------|
| **模型解耦** | BaseLanguageModel 统一抽象层 |
| **上下文组织** | PromptTemplate + Memory 系统 |
| **流程编排** | Runnable + LCEL 链式组合 |
| **Agent决策** | AgentExecutor + ReAct 循环 |
| **知识检索** | RAG + VectorStore |
| **图式工作流** | LangGraph 状态图 |

---

## 核心抽象

### Runnable

所有组件的统一抽象单元：

```python
class Runnable:
    def invoke(self, input) -> Output    # 单次执行
    def stream(self, input) -> Iterator  # 流式输出
    def batch(self, inputs) -> List      # 批量处理
```

### LCEL

LangChain Expression Language，用 `|` 运算符组合 Runnable：

```python
chain = prompt | llm | StrOutputParser()
```

---

## 包结构

```
langchain-core      → 核心抽象（Runnable、LCEL）
langchain           → 高级组合模块
langchain-community → 社区扩展
langgraph           → 图式 Agent 工作流
langserve           → REST API 部署
langsmith           → 可观测化平台
```

---

## 关键特性

| 特性 | 说明 |
|------|------|
| **统一接口** | 所有组件遵循 Runnable 协议 |
| **链式组合** | LCEL 支持 `|` 运算符拼接 |
| **多模型适配** | OpenAI/Anthropic/DeepSeek 等无缝切换 |
| **持久化** | LangGraph 支持 checkpoint 中断恢复 |
| **可观测** | LangSmith 提供日志追踪和性能评估 |

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain 设计原理详解
- [[concepts/ai-agent]] - Agent 智能体
- [[concepts/rag]] - RAG 检索增强生成
- [[concepts/lcel]] - LCEL 表达式语言