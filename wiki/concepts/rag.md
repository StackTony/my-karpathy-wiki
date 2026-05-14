---
title: RAG 检索增强生成
created: 2026-05-13
updated: 2026-05-13
tags: [AI, RAG, 检索, LLM]
---

# RAG 检索增强生成

RAG（Retrieval-Augmented Generation）是"检索 + 生成"的组合架构，解决 LLM 知识局限问题。

---

## 核心流程

```
用户问题 → Embedding → 向量检索 → 获取相关文档 → 构建 Prompt → LLM 生成 → 返回答案
```

---

## 关键组件

| 组件 | 说明 |
|------|------|
| **Retriever** | 检索器，返回相关文档列表 |
| **VectorStore** | 向量数据库（FAISS、Chroma） |
| **Embedding** | 文本向量化模型 |
| **ChainType** | 文档合并策略 |

---

## ChainType 合并策略

| 类型 | 说明 | 适用场景 |
|------|------|----------|
| `stuff` | 直接拼接所有片段 | 简单场景 |
| `map_reduce` | 分别生成再汇总 | 大量文档 |
| `refine` | 迭代精炼答案 | 追求精确 |

---

## LangChain RAG 实现

```python
from langchain.chains.retrieval import create_retrieval_chain

# 1. 文档切分
texts = splitter.split_documents(raw_docs)

# 2. 向量索引
vectorstore = FAISS.from_documents(texts, embeddings)

# 3. 构建 Chain
retriever = vectorstore.as_retriever(search_kwargs={"k": 2})
chain = create_retrieval_chain(retriever, combine_docs_chain)

# 4. 执行
result = chain.invoke({"input": "问题"})
```

---

## Retriever 类型

| 类型 | 说明 |
|------|------|
| VectorStoreRetriever | 基于向量相似度 |
| MultiQueryRetriever | 多角度查询扩展 |
| ContextualCompressionRetriever | 上下文压缩 |

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain RAG 章节
- [[concepts/langchain]] - LangChain 框架
- [[concepts/vectorstore]] - 向量数据库