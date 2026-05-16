---
title: Chunk策略
created: 2026-05-16
updated: 2026-05-16
tags: [RAG, Chunk策略, 分块算法, 递归分块, 语义分块]
---

# Chunk策略

Chunk策略（分块策略）是RAG系统中将长文本切分为适合检索的语义单元的方法，直接影响召回率和相关性。

## 五层分级（Greg Kamradt框架）

```
Level 1: Character Splitting      → 固定字符切分，无语义感知
Level 2: Recursive Character      → 生产默认，分隔符层级切分
Level 3: Document-Structure       → Markdown/HTML结构感知
Level 4: Semantic Chunking        → 嵌入相似度检测话题转变
Level 5: Agentic Chunking         → LLM判断分块边界
```

## 生产默认配置

- **策略**：RecursiveCharacterTextSplitter
- **大小**：256-512 token
- **重叠**：10-15%
- **效果**：85-90% Recall@5

## 关键反模式

- 重叠>20%有害（索引膨胀、精度下降）
- 语义分块需200+ token最小限制
- 异构语料需按类型分块

## 关联

- [[summaries/chunk-strategies-deep-dive]] - Chunk策略深入详解
- [[concepts/rag]] - RAG基础概念
- [[concepts/langchain]] - LangChain框架（RecursiveCharacterTextSplitter）