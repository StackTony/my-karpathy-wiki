---
title: RRF 算法 - 倒数排名融合
created: 2026-05-16
updated: 2026-05-16
tags: [RRF, 检索, 混合检索, RAG, 算法]
---

# RRF 算法 - 倒数排名融合

RRF（Reciprocal Rank Fusion）是信息检索中合并多路搜索结果的标准算法。

---

## 核心思想

**仅凭排名位置，无需分数归一化，合并多路结果为统一列表。**

---

## 解决的问题

混合检索场景痛点：

| 问题 | 说明 |
|------|------|
| **量纲不同** | BM25分数0.1-5.0，向量分数0.6-0.95 |
| **归一化困难** | Min-Max对异常值敏感 |
| **缺乏可比性** | 不同列表的相同分数代表不同置信度 |

---

## 计算公式

$$RRF(d) = \sum_{r \in R} \frac{1}{k + rank_r(d)}$$

| 参数 | 说明 | 经验值 |
|------|------|--------|
| $k$ | 平滑常数，防止分母为零 | **60** |
| $rank_r(d)$ | 文档d在列表r中的排名 | 从1开始 |

---

## 计算示例

两个检索列表：

| 排名 | BM25 | 向量检索 |
|------|------|----------|
| 1 | D1 | D3 |
| 2 | D2 | D1 |
| 3 | D3 | D4 |
| 4 | D5 | D2 |
| 5 | D4 | D6 |

RRF得分（k=60）：

| 文档 | 计算 | 得分 |
|------|------|------|
| D1 | 1/(60+1) + 1/(60+2) | **0.0325** |
| D3 | 1/(60+3) + 1/(60+1) | **0.0323** |
| D2 | 1/(60+2) + 1/(60+4) | **0.0317** |
| D4 | 1/(60+5) + 1/(60+3) | **0.0313** |
| D5 | 1/(60+5) | **0.0154** |

**共识机制**：多列表出现的文档得分远高于单列表文档。

---

## 核心优势

| 优势 | 说明 |
|------|------|
| **无需归一化** | 只要有序列表就能工作 |
| **鲁棒性强** | 对异常分数不敏感 |
| **简单高效** | 仅倒数和加法 |
| **扩展性好** | 支持2路或N路融合 |

---

## 应用场景

| 场景 | 说明 |
|------|------|
| **混合检索** | BM25 + 向量检索结果合并 |
| **RAG-Fusion** | 多查询扩展后结果合并 |
| **元搜索** | 多搜索引擎结果合并 |

---

## Python 实现

```python
def reciprocal_rank_fusion(lists_of_docs, k=60):
    rrf_scores = {}
    for doc_list in lists_of_docs:
        for rank, doc_id in enumerate(doc_list):
            current_rank = rank + 1
            if doc_id not in rrf_scores:
                rrf_scores[doc_id] = 0
            rrf_scores[doc_id] += 1 / (k + current_rank)
    return sorted(rrf_scores.items(), key=lambda x: x[1], reverse=True)
```

---

## 相关链接

- [[summaries/rag-full-stack-introduction]] - RAG 全栈介绍
- [[summaries/rerank-production-practice]] - Rerank 生产实践
- [[concepts/rag]] - RAG 基础概念