---
title: Linux Mutex（互斥锁）
created: 2026-05-11
updated: 2026-05-11
tags: [mutex, kernel, sync, lock]
sources: [Linux Mutex锁]
---

## 定义

Mutex（互斥锁）是Linux内核中最常用的睡眠锁，竞争失败时进程睡眠，可被调度器切换。

## 核心特性

| 特性 | 说明 |
|------|------|
| 睡眠锁 | 竞争失败时进程睡眠 |
| 仅进程上下文 | 不可用于中断 |
| 乐观自旋 | 持锁时间短时先自旋再睡眠 |
| 优先级继承 | 支持PI，避免优先级反转 |
| 所有权语义 | 只有持有者才能释放 |

## 与Spinlock对比

| 特性 | Mutex | Spinlock |
|------|-------|----------|
| 竞争时行为 | 睡眠等待 | 忙等待（自旋） |
| 上下文限制 | 仅进程上下文 | 任意上下文 |
| 持锁时间 | 可长时间持有 | 必须极短 |
| 中断中使用 | ❌ 禁止 | ✓ 配合_irq版本 |

## 数据结构

```c
struct mutex {
    atomic_long_t owner;      // 持有者 + 标志位
    spinlock_t wait_lock;     // 保护等待队列
    struct list_head wait_list; // 等待者队列
    struct optimistic_spin_queue osq; // MCS锁（乐观自旋）
};
```

### Owner字段编码

```
┌─────────────────────────────────────────────┬─────┐
│         task_struct 指针 (高位)              │flags│
└─────────────────────────────────────────────┴─────┘
                              │
         0x01: MUTEX_FLAG_WAITERS (有等待者)
         0x02: MUTEX_FLAG_HANDOFF  (移交锁)
```

## API

```c
// 定义与初始化
DEFINE_MUTEX(name);
void mutex_init(struct mutex *lock);

// 锁获取
void mutex_lock(struct mutex *lock);
int mutex_lock_interruptible(struct mutex *lock);  // 可被信号中断
int mutex_trylock(struct mutex *lock);             // 非阻塞

// 锁释放
void mutex_unlock(struct mutex *lock);
```

## 乐观自旋

当满足条件时，Mutex先短时间自旋等待，仍失败才加入等待队列：

- 持有者正在运行（其他CPU）
- 只有一个等待者
- 自旋次数未超限

优势：如果持有者很快释放，避免上下文切换开销（~5-10μs）

## 使用示例

```c
// ✓ 正确：进程上下文使用
void process_context_func(void) {
    mutex_lock(&lock);
    copy_from_user(buf, ...);   // 可以睡眠
    mutex_unlock(&lock);
}

// ✗ 错误：中断上下文使用
irqreturn_t irq_handler(int irq, void *dev) {
    mutex_lock(&lock);          // 死锁！
    mutex_unlock(&lock);
    return IRQ_HANDLED;
}
```

## Crash调试

```bash
# 解码owner获取持有者（清除低3位）
crash> p/x ((struct mutex *)<addr>)->owner.counter & ~0x7
crash> struct task_struct.comm <decoded_addr>
```

## 参考

- [[summaries/linux-lock-mechanisms]]
- [[concepts/linux-spinlock]]
- [[concepts/linux-rcu]]