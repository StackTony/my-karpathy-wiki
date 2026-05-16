---
title: Runnable LangChain 抽象单元
created: 2026-05-16
updated: 2026-05-16
tags: [LangChain, Runnable, LCEL, LLM]
---

# Runnable LangChain 抽象单元

Runnable 是 LangChain 所有组件的统一抽象接口，支持链式组合和流式处理。

---

## 定义

Runnable 是 LangChain 中所有可执行组件的基础抽象：

```python
class Runnable:
    def invoke(self, input) -> Output       # 单次执行
    def stream(self, input) -> Iterator      # 流式输出
    def batch(self, inputs) -> List          # 批量处理
    def transform(self, input) -> Output     # 输出转换
```

---

## 核心方法

| 方法 | 说明 | 用途 |
|------|------|------|
| `.invoke()` | 同步单次执行 | 获取单个结果 |
| `.stream()` | 流式迭代输出 | 实时显示生成过程 |
| `.batch()` | 并行批量处理 | 处理多个输入 |
| `.with_retry()` | 自动重试 | 错误恢复 |
| `.with_fallbacks()` | 备用路径 | 降级策略 |

---

## LCEL 链式组合

通过 `|` 运算符将 Runnable 组合成管道：

```python
# 简单链
chain = prompt | llm | StrOutputParser()

# 等价于
chain = RunnableSequence([prompt, llm, StrOutputParser()])
```

---

## Runnable 类型

| 类型 | 说明 |
|------|------|
| `RunnableSequence` | 顺序执行链 |
| `RunnableParallel` | 并行执行多个分支 |
| `RunnableBranch` | 条件分支选择 |
| `RunnableLambda` | 自定义函数包装 |
| `RunnableMap` | 多输出映射 |

---

## 示例

```python
# 自定义 Runnable
class AddOne(Runnable[int, int]):
    def invoke(self, input: int, config=None) -> int:
        return input + 1

# 链式组合
workflow = AddOne() | RunnableLambda(lambda x: x * x)
workflow.invoke(2)  # 输出：9
```

---

## 继承关系

```
Runnable → RunnableSerializable → 具体实现
                ↓
         自动获得 batch/stream/with_retry 等方法
```

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain 架构详解
- [[concepts/lcel]] - LCEL 表达式语言
- [[concepts/langchain]] - LangChain 概念