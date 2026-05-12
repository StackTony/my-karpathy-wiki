---
title: Linux 进程调度器
created: 2026-05-12
updated: 2026-05-12
tags: [Linux, 进程调度, CFS, vruntime]
sources: [Linux操作系统/Linux进程调度]
---

# Linux 进程调度器

Linux内核调度器负责决定哪个进程获得CPU时间以及何时进行进程切换。

## 调度器类型

| 类型 | 说明 |
|------|------|
| 主调度器 | 进程主动放弃CPU（睡眠等） |
| 周期性调度器 | 固定频率检测是否需要切换 |

## 调度策略

| 策略 | 类型 | 说明 |
|------|------|------|
| SCHED_OTHER | 分时调度 | 默认策略，基于nice和counter |
| SCHED_FIFO | 实时调度 | 先到先服务，无时间片 |
| SCHED_RR | 实时调度 | 时间片轮转 |

**实时进程优先调度**，分时进程通过nice（越小越优先）和counter（使用CPU少的优先）决定。

## CFS 完全公平调度器

### 核心机制

| 组件 | 说明 |
|------|------|
| 调度实体 | 进程（task_struct）或线程 |
| 红黑树 | 按vruntime排序的可运行进程 |
| vruntime | 虚拟运行时间（考虑执行时间+优先级） |

### 调度流程

```
schedule()调用:
├── 当前进程标记不可运行
├── 从红黑树选择最小vruntime进程
├── 上下文切换（保存/加载寄存器）
└── 新进程开始执行
```

### 关键概念

| 概念 | 说明 |
|------|------|
| 抢占 | 高优先级可中断低优先级 |
| 调度域 | CPU组，共享策略和优先级队列 |
| 等待队列 | 等待特定事件的进程管理 |
| 负载均衡 | CPU间平均分配进程 |

## 进程状态

| 状态 | 说明 |
|------|------|
| R (Running) | 可运行 |
| D (等待) | 不可中断等待 |
| T (停止) | 被暂停 |
| Z (僵尸) | 已终止但未回收 |

调度器只关心可运行进程。

## CFS核心代码

```c
// 调度器入口
schedule() → pick_next_task_fair()
    → 从红黑树选最小vruntime进程

// 周期性调度
scheduler_tick() → 更新vruntime、检查抢占
```

## nice值影响

nice值范围：-20到19（默认0）
- nice越小，优先级越高
- nice影响vruntime增长速度

## 相关链接

- [[concepts/cfs-scheduler]]
- [[concepts/linux-scheduling-policy]]
- [[concepts/task-struct]]