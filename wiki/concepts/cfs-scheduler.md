---
title: CFS 完全公平调度器
created: 2026-05-12
updated: 2026-05-13
tags: [Linux, 调度器, CFS, vruntime, 红黑树]
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

## 红黑树的作用

CFS使用[[concepts/red-black-tree]]作为核心数据结构：

| 操作 | 红黑树优势 |
|------|-----------|
| 插入新进程 | O(logn)，进程创建频繁 |
| 删除进程 | O(logn)，进程退出频繁 |
| 查找最小vruntime | O(1)，左子树最深处 |
| 旋转调整 | ≤1次，比AVL树更高效 |

**为什么选择红黑树而非AVL树？**
- 进程调度频繁插入/删除，红黑树O(1)旋转更优
- 不需要AVL的严格平衡，弱平衡足够

## Cgroups CPU子系统关联

Cgroups 的 cpu 子系统使用 CFS 实现 CPU 限制：

```
cpu.cfs_period_us = 100000  (100ms周期)
cpu.cfs_quota_us  = 50000   (50ms配额)
→ 进程最多使用50% CPU
```

与 [[concepts/cgroups]] 配合实现容器CPU限制。

## 相关链接

- [[summaries/linux-scheduler]]
- [[concepts/linux-scheduling-policy]]
- [[concepts/red-black-tree]] - 数据结构基础
- [[summaries/red-black-tree-detailed]] - 红黑树详解
- [[concepts/cgroups]] - Cgroups CPU限制