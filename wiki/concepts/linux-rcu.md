---
title: Linux RCU（Read-Copy-Update）
created: 2026-05-11
updated: 2026-05-11
tags: [rcu, kernel, sync, lock-free]
sources: [Linux RCU锁]
---

## 定义

RCU（Read-Copy-Update）是Linux内核中最独特的同步机制，实现了"读者零开销"的极致性能。

## 核心思想

```
传统锁的困境:
  读者: lock → 读 → unlock（即使无冲突也有开销）
  
RCU的突破:
  读者: 直接读（无锁！）→ 读侧几乎零开销
  写者: 复制 → 修改副本 → 替换指针 → 等待宽限期 → 释放旧数据
```

> 关键洞察：读操作不需要与写操作同步，只需要看到一致的数据版本

## 与其他锁对比

| 特性 | Spinlock | Mutex | RCU |
|------|----------|-------|-----|
| 读侧开销 | 有 | 有 | 几乎为零 |
| 写侧开销 | 小 | 中等 | 较大 |
| 上下文限制 | 任意 | 仅进程 | 任意（读侧） |
| 适用场景 | 读写对称 | 读写对称 | 读极多写极少 |

## 工作原理

### 宽限期（Grace Period）

从写者替换指针开始，到所有旧读者退出临界区的时间段。

```
时间轴:
  t0: 写者替换指针
  t0-t5: 宽限期（等待旧读者退出）
  t5: 释放旧数据
```

静止点（Quiescent State）：上下文切换、idle状态、用户态执行

### 写者流程

```
1. 复制旧数据: new = kmalloc(); *new = *old;
2. 修改副本: new->field = new_value;
3. 替换指针: rcu_assign_pointer(global_ptr, new);
4. 等待宽限期: synchronize_rcu();
5. 释放旧数据: kfree(old);
```

### 读者流程

```c
rcu_read_lock();        // preempt_disable()
p = rcu_dereference(global_ptr);  // 带内存屏障读取
val = p->field;         // 只读访问
rcu_read_unlock();      // preempt_enable()
```

总开销：约15-30 CPU cycles（接近零）

## RCU变体

| 变体 | 读侧特点 | 适用场景 |
|------|----------|----------|
| Classic RCU | 不可睡眠 | 内核通用同步 |
| SRCU | 可睡眠 | 需要在读侧睡眠 |
| RCU-sched | 禁用抢占 | 调度器相关 |
| RCU-bh | 禁用软中断 | 网络软中断路径 |

## API

```c
// 读侧
void rcu_read_lock(void);
void rcu_read_unlock(void);
#define rcu_dereference(p)    // 带内存屏障读取

// 写侧
void synchronize_rcu(void);   // 阻塞等待宽限期
void call_rcu(struct rcu_head *head, rcu_callback_t func);  // 异步回调
#define rcu_assign_pointer(p, v)  // 带内存屏障赋值

// 简化释放
void kfree_rcu(ptr, rcu_field);
```

## 适用条件

经验法则：
- R/W > 100：RCU明显优于传统锁
- R/W > 10：RCU可能优于传统锁
- R/W < 10：需要具体分析
- R/W < 1：传统锁更合适

## 内核典型应用

- VFS：dentry缓存、inode查找
- 网络：路由表、设备列表
- 进程管理：PID查找、cgroup
- 安全：LSM钩子、SELinux策略

## 常见错误

| 错误 | 说明 |
|------|------|
| 读侧修改数据 | RCU只适用读操作 |
| 过早释放数据 | 必须等待宽限期后释放 |
| 经典RCU中睡眠 | 使用SRCU替代 |
| 忘记rcu_dereference | 缺少内存屏障 |
| 忘记rcu_assign_pointer | 缺少内存屏障 |

## 参考

- [[summaries/linux-lock-mechanisms]]
- [[concepts/linux-mutex]]
- [[concepts/linux-spinlock]]