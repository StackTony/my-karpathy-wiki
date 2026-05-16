---
title: Rerank重排序的企业实践
source_url: https://ithelp.ithome.com.tw/m/articles/10377060
source_site: iT 邦幫忙
author: dallen12151830
date_extracted: 2026-05-16
credibility: low
tags: [RAG, Rerank, Bi-encoder, Cross-encoder, 检索精度]
---

# Rerank重排序的企业实践

在 RAG 的检索阶段，我们会取得数个与使用者问题相關的 chunk，並按照相似度排序。但問題是：

- **ANN 雖然快，但可能不準確**（尤其在百萬級知識庫中，結果常有雜訊）
- 因此我們需要 **Rerank 技術**，在候選集合中進行「精修」，提升檢索精度

這就是企業常用的 **二階段檢索流程**：

1. **粗修**：ANN 快速檢索 → 取前 100 筆候選
2. **精修**：Rerank 模型對候選進行排序 → 取前 5–10 筆餵給 LLM

---

## Rerank 模型原理

Rerank 模型通常訓練成 **相關性評分任務**：

- **輸入**：一個 Query（問題） + 一個 Document（候選文檔）
- **輸出**：0-1 之間的相關性分數（分數越高表示越相關）

Cross-encoder 模型會將 Query 和 Document 拼接後，通過 Transformer 架構計算深層語義相關度。

---

## Bi-encoder vs Cross-encoder 對比

| 模型類型 | 工作方式 | 優點 | 缺點 | 適用場景 |
|---------|----------|------|------|----------|
| **Bi-encoder** | Query & Document 各自編碼成向量 → 相似度比對 | 檢索快、可 ANN 加速 | 語義理解有限 | 初步檢索 |
| **Cross-encoder** | Query + Document 拼在一起 → Transformer 計算相關性 | 精度高、上下文理解強 | 計算成本高、無法預先索引 | Rerank 重排序 |

**關鍵**：企業常用 **Bi-encoder + ANN** 作粗修，再用 **Cross-encoder** 作精修。

---

## Rerank 策略

### 1. 標準二階段檢索

```
Query → Bi-encoder 向量化 → ANN 索引檢索 → Top-100 候選 → Cross-encoder 重排 → Top-10 結果
```

### 2. 混合模型策略

```
Query → 快速Bi-encoder → ANN 粗排 → Top-100 → 精確Cross-encoder → Top-10
```

### 3. 分層檢索策略

```
Query → ANN 檢索 → Top-1000 → 第一輪Rerank → Top-100 → 第二輪Rerank → Top-10
```

---

## 效果評估指標

- **Recall@K**：在前 K 筆結果中，正確答案被找回的比例
- **MRR (Mean Reciprocal Rank)**：越早找到正確答案，分數越高
- **nDCG**：考慮排序位置的加權分數

### 實際效果：

- 單用 ANN → Recall@10 = 60-75%
- 加上 Rerank → Recall@10 = 75-90%

通常能帶來 **10-30% 的精度提升**。

---

## 實務考量

### 延遲 vs 精度權衡

```
高精度需求（法務、醫療）
├─ Cross-encoder 重排（可接受較高延遲）

一般業務需求  
├─ 混合策略（平衡精度與速度）

高頻查詢場景（客服、搜尋）
└─ 快速 Bi-encoder（優先考慮速度）
```

### 成本控制策略

- **批次重排**：累積查詢後批次處理
- **快取機制**：常見查詢結果快取
- **分層服務**：VIP 用戶使用精排

---

## 總結

Rerank 是讓 RAG **從「能用」到「好用」的關鍵技術**：

- **ANN** → 快速檢索，解決「找不到」問題
- **Rerank** → 精確排序，解決「找不準」問題
- **企業最佳實踐**：二階段檢索管線（ANN 粗修 + Cross-encoder 精修）

---

*来源：iT 邦幫忙 | 作者：dallen12151830 | 提取日期：2026-05-16*