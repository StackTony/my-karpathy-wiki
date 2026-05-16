---
title: ksoftirqd 内核线程
created: 2026-05-16
updated: 2026-05-16
tags: [Linux, ksoftirqd, softirq, 内核线程]
---

# ksoftirqd 内核线程

ksoftirqd 是每个 CPU 对应的软中断处理内核线程，用于在高负载时接管 softirq 处理。

---

## 基本信息

| 属性 | 说明 |
|------|------|
| 类型 | 内核线程 |
| 数量 | 每个 CPU 一个 |
| 命名 | ksoftirqd/CPU编号 |
| 优先级 | 低优先级（不影响用户进程） |

---

## 触发条件

当 `__do_softirq()` 循环无法处理完所有软中断时唤醒：

| 条件 | 值 | 说明 |
|------|-----|------|
| MAX_SOFTIRQ_TIME | 2 jiffies | 最大执行时间约10ms |
| MAX_SOFTIRQ_RESTART | 10 | 最大循环次数 |
| need_resched() | - | 有进程需要调度 |

---

## 工作流程

```
__do_softirq() 主循环
    ↓
条件检查：超时 或 循环耗尽 或 需调度
    ↓
wakeup_softirqd()
    ↓
ksoftirqd 线程被唤醒
    ↓
run_ksoftirqd() → __do_softirq()
    ↓
低优先级执行，不影响用户进程
```

---

## 查看

```bash
$ ps aux | grep ksoftirqd
root  3  [ksoftirqd/0]
root  9  [ksoftirqd/1]
root 12  [ksoftirqd/2]
root 15  [ksoftirqd/3]
```

---

## 设计目的

解决软中断重调度问题：
- **立即执行**：可能饿死用户进程
- **延后执行**：可能丢失实时性
- **线程接管**：当前方案，负载高时线程处理

---

## 相关链接

- [[concepts/softirq]] - 软中断概念
- [[summaries/linux-softirq-mechanism]] - 软中断机制详解
- [[summaries/linux-scheduler]] - 内核线程调度