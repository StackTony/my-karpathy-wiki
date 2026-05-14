---
title: Prompt 提示词工程
created: 2026-05-13
updated: 2026-05-13
tags: [AI, Prompt, 提示词]
source_dir: AI人工智能/Prompt + RAG
source_files: [Prompt 提示词.md]
---

# Prompt 提示词工程

Prompt 是与 LLM 交互的核心方式，通过精心设计的提示词引导模型生成期望的输出。

---

## Prompt 模板结构

一个标准的 Prompt 模板包含以下要素：

```markdown
【角色】你是一个资深的xxx（代码架构分析/数据分析）专家，同时也是xx方面的专家，精通xxx能力

【任务】
1、需要分析xxx代码目录：xx
2、需要使用SKILL：xx
3、需要拆解大型任务为多个顺序子任务，最终完成xxx的任务：xx
```

### 模板要素

| 要素 | 说明 | 作用 |
|------|------|------|
| **角色定义** | 设定专家身份 | 引导模型输出专业内容 |
| **任务描述** | 明确目标任务 | 限定输出范围 |
| **技能调用** | 指定使用工具 | 扩展 Agent 能力 |
| **任务拆解** | 分解子任务 | 复杂任务简化处理 |

---

## PromptTemplate 与 LangChain

在 LangChain 中，PromptTemplate 提供动态变量注入：

```python
# 简单模板
template = PromptTemplate.from_template("请回答：{question}")

# 多角色对话模板
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个{role}专家"),
    ("human", "{question}")
])
```

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain PromptTemplate 详解
- [[concepts/prompt-engineering]] - Prompt 工程概念