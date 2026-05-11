---
title: Linux Spinlock（自旋锁）
created: 2026-05-11
updated: 2026-05-11
tags: [spinlock, kernel, sync, lock]
sources: [Linux SpinLock锁]
---

## 定义

Spinlock（自旋锁）是Linux内核中的"忙等待"锁，获取失败时不睡眠，持续检查锁状态并占用CPU。

## 核心原则

> **"快进快出，绝不睡眠"**

适用条件：
- 持锁时间极短（通常 < 100条指令，微秒级）
- 等待时间 < 上下文切换开销（~5-10μs）
- 可用于任意上下文（中断、软中断、进程）

## 实现演进

### 第一代：Test-And-Set锁
- 无公平性，可能导致饥饿
- 后来等待者可能先获得锁

### 第二代：Ticket Lock（排队锁）
- FIFO公平性，按获取顺序排队
- 所有等待者检查同一owner变量
- 缓存行竞争严重

### 第三代：MCS/Queued Spinlock（现代Linux）
- 每个CPU在本地节点上自旋
- 通过链表传递锁，避免缓存行竞争
- NUMA系统性能优异

## API矩阵

```
              基础版本    禁用中断版本    禁用下半部版本
─────────────────────────────────────────────────────────
非保存/恢复   spin_lock    spin_lock_irq   spin_lock_bh
保存/恢复     -            spin_lock_irqsave    -
```

### 变体选择

| 场景 | 选择API |
|------|---------|
| 与硬件中断共享数据 | spin_lock_irqsave |
| 与软中断共享数据 | spin_lock_bh |
| 无共享（纯进程上下文） | spin_lock |
| 已在中断上下文中 | spin_lock（无需再禁用） |

## 性能开销

- spin_lock: ~50-100 CPU cycles
- spin_unlock: ~10-20 CPU cycles

## 最佳实践

```c
// ✓ 最小临界区
spin_lock(&lock);
shared_var++;
spin_unlock(&lock);

// ✓ 使用 irqsave 而非 irq
unsigned long flags;
spin_lock_irqsave(&lock, flags);
/* ... */
spin_unlock_irqrestore(&lock, flags);

// ✗ 错误：在spinlock中睡眠
spin_lock(&lock);
kmalloc(size, GFP_KERNEL);  // 可能睡眠！
spin_unlock(&lock);
```

## raw_spinlock vs spinlock

| 类型 | 特点 | 用途 |
|------|------|------|
| raw_spinlock | 绝不睡眠，不受PREEMPT_RT影响 | 调度器、中断核心代码 |
| spinlock | RT内核下自动变为rt_mutex | 一般内核代码 |

## 参考

- [[summaries/linux-lock-mechanisms]]
- [[concepts/linux-mutex]]