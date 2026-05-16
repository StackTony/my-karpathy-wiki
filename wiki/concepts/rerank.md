---
title: Rerank重排序
created: 2026-05-16
updated: 2026-05-16
tags: [RAG, Rerank, Bi-Encoder, Cross-Encoder, 二阶段检索]
---

# Rerank重排序

Rerank（重排序）是RAG系统中对第一阶段召回结果进行精确排序的技术，用于提升检索精度。

## 核心原理

**二阶段检索**：
1. **粗排（Bi-Encoder）**：快速向量检索，Top 50-100候选
2. **精排（Cross-Encoder）**：Query+Doc联合编码，Top 5-10输出

## Bi-Encoder vs Cross-Encoder

| 类型 | 精度 | 速度 | 特点 |
|------|------|------|------|
| Bi-Encoder | 70-80% | 快 | 独立编码，可预索引，ANN加速 |
| Cross-Encoder | 95%+ | 慢 | 联合编码，词级交互，无法预索引 |

## 效果提升

- 单ANN：60-75% Recall@10
- ANN + Rerank：75-90% Recall@10

## 融合策略

- **RRF**：倒数排名融合，只看排名不看分数
- **WeightedRanker**：分数加权平均，需归一化

## 关联

- [[summaries/rerank-production-practice]] - Rerank生产实践详解
- [[concepts/rrf-algorithm]] - RRF融合算法
- [[concepts/rag]] - RAG基础概念