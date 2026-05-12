---
title: CFS 完全公平调度器
created: 2026-05-12
updated: 2026-05-12
tags: [Linux, 调度器, CFS, vruntime]
---

# CFS 完全公平调度器

Linux默认的进程调度器，基于红黑树和虚拟运行时间实现公平调度。

## 核心设计

**目标**：让所有进程获得公平的CPU时间。

## 关键机制

| 机制 | 说明 |
|------|------|
| 红黑树 | 按vruntime排序可运行进程 |
| vruntime | 虚拟运行时间，越小越优先 |
| min_vruntime | 树中最小vruntime，新进程基准 |

## vruntime计算

```
vruntime += 实际运行时间 × (优先级权重因子)
```

- nice值越小，权重越大，vruntime增长越慢
- 优先级高的进程vruntime增长慢，获得更多CPU

## 调度决策

**选择下一个进程**：红黑树最左侧节点（最小vruntime）

## 相关链接

- [[summaries/linux-scheduler]]
- [[concepts/linux-scheduling-policy]]