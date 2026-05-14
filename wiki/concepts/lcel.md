---
title: LCEL LangChain 表达式语言
created: 2026-05-13
updated: 2026-05-13
tags: [AI, LangChain, LCEL, Runnable]
---

# LCEL LangChain 表达式语言

LCEL（LangChain Expression Language）是声明式编排语言，用 `|` 运算符将 Runnable 组合成数据流管道。

---

## 核心概念

| 特性 | 说明 |
|------|------|
| **链式组合** | `|` 运算符拼接 Runnable |
| **统一接口** | invoke/stream/batch 自动继承 |
| **类型安全** | 输入输出类型检查 |

---

## 运算符重载

通过 `__or__()` 方法实现链式组合：

```python
chain = prompt | llm | StrOutputParser()

# 等价于
chain = RunnableSequence([prompt, llm, StrOutputParser()])
```

---

## Runnable 扩展

| 扩展方法 | 说明 |
|----------|------|
| `RunnableParallel` | 并行执行多个 Runnable |
| `RunnableBranch` | 条件分支选择 |
| `RunnableLambda` | 自定义函数包装 |
| `RunnableMap` | 多输出映射 |

---

## 完整示例

```python
# LCEL 链式组合
chain = (
    RunnableMap({"name": lambda _: "LangChain"})
    | RunnableLambda(lambda d: f"Hello, {d['name']}!")
)

# 执行
chain.invoke({})  # 输出：Hello, LangChain!
```

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain LCEL 章节
- [[concepts/langchain]] - LangChain 框架
- [[concepts/runnable]] - Runnable 抽象