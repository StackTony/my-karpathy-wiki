---
title: Transformer 模型
created: 2026-05-13
updated: 2026-05-13
tags: [AI, Transformer, LLM, 深度学习]
source_dir: AI人工智能/大模型
source_files: [Transformer模型.md]
---

# Transformer 模型

Transformer 是现代大语言模型（LLM）的基础架构。

---

## 核心机制

| 机制 | 说明 |
|------|------|
| **Self-Attention** | 自注意力机制，捕捉序列内部关系 |
| **Multi-Head** | 多头注意力，多角度关注信息 |
| **Position Encoding** | 位置编码，保留序列顺序信息 |
| **Feed-Forward** | 前馈网络，非线性变换 |

---

## 学习资源

| 资源 | 链接 | 说明 |
|------|------|------|
| Transformer原理详解 | https://zhuanlan.zhihu.com/p/338817680 | 知乎专栏 |

---

## 与 LLM 关系

现代 LLM（GPT、Claude、LLaMA）均基于 Transformer架构：

```
Transformer → Encoder-Decoder → GPT（Decoder-only）→ 现代LLM
```

---

## 相关链接

- [[concepts/langchain]] - LangChain 框架（LLM调用层）
- [[concepts/ai-agent]] - Agent 智能体