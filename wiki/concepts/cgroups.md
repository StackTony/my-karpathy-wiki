---
title: Cgroups
created: 2026-05-13
updated: 2026-05-13
tags: [Linux, 容器, 资源限制]
---

# Cgroups

## 定义

Linux 内核限制、记录、隔离进程组物理资源的机制。

## 核心功能

| 功能 | 说明 |
|------|------|
| 资源限制 | 设定CPU/内存/IO上限 |
| 优先级 | 分配资源份额 |
| 统计 | 记录使用情况 |
| 控制 | 挂起/恢复进程 |

## 主要子系统

| 子系统 | 资源 |
|--------|------|
| cpu | CPU时间份额 |
| memory | 内存上限（触发OOM） |
| blkio | 块设备IO |
| cpuset | CPU核心绑定 |
| devices | 设备访问控制 |

## 使用方式

```bash
# 创建控制组
mkdir /sys/fs/cgroup/memory/test

# 设置限制
echo 104857600 > test/memory.limit_in_bytes

# 加入进程
echo $PID > test/tasks
```

## CPU限制原理

```
cpu.cfs_period_us = 100000  (100ms周期)
cpu.cfs_quota_us  = 50000   (50ms配额)
→ 进程最多使用50% CPU
```

与 [[concepts/cfs-scheduler]] 配合工作。

## 与容器关系

```
容器限制 = Cgroups(能用多少) + Namespace(看见什么)
```

## 关联

- [[summaries/linux-namespace-cgroups]] - 完整介绍
- [[concepts/namespace]] - 资源隔离
- [[summaries/linux-scheduler]] - cpu子系统
- [[summaries/linux-meminfo]] - memory子系统OOM
- [[concepts/containerd]] - 容器运行时