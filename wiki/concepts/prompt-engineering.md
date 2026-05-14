---
title: Prompt Engineering 提示词工程
created: 2026-05-13
updated: 2026-05-13
tags: [AI, Prompt, 提示词, LLM]
---

# Prompt Engineering 提示词工程

Prompt Engineering 是设计提示词以引导 LLM 生成期望输出的技术。

---

## Prompt 结构要素

| 要素 | 说明 | 作用 |
|------|------|------|
| **角色定义** | 设定专家身份 | 引导专业输出 |
| **任务描述** | 明确目标任务 | 限定输出范围 |
| **技能调用** | 指定使用工具 | 扩展 Agent 能力 |
| **任务拆解** | 分解子任务 | 复杂任务简化 |

---

## PromptTemplate

LangChain 动态模板系统：

```python
from langchain.prompts import PromptTemplate, ChatPromptTemplate

# 简单模板
template = PromptTemplate.from_template("请回答：{question}")

# 多角色对话
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个{role}专家"),
    ("human", "{question}")
])
```

---

## 高级技巧

| 技巧 | 说明 |
|------|------|
| **Few-shot** | 提供示例引导输出格式 |
| **Chain-of-Thought** | 引导模型逐步推理 |
| **ReAct** | Think-Act-Observe 循环 |

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain PromptTemplate 章节
- [[summaries/prompt-engineering]] - Prompt 提示词工程摘要
- [[concepts/langchain]] - LangChain 框架