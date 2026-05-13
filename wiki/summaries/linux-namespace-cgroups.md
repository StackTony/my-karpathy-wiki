---
title: Linux Namespace 与 Cgroups 资源隔离
created: 2026-05-13
updated: 2026-05-13
tags: [Linux, Namespace, Cgroups, 容器, 资源隔离]
source_dir: Linux操作系统/Linux 资源隔离
source_files: [Linux Namespace与Cgroups介绍.md]
---

# Linux Namespace 与 Cgroups 资源隔离

Linux 容器技术的两大基石：Namespace 实现资源隔离，Cgroups 实现资源限制。

---

## 一、Namespace 概念

**Namespace** 是 Linux 内核用来隔离内核资源的方式，使不同进程只能看到与自己相关的资源，形成独立的"视图"。

### 核心用途

- **轻量级虚拟化**：容器技术的基础
- **资源隔离**：进程树、网络、文件系统、用户权限等
- **安全隔离**：不同用户/容器互不干扰

### Namespace 类型

| 类型 | 隔离资源 | 内核版本 | 说明 |
|------|----------|----------|------|
| **PID** | 进程ID | 2.6 | 容器有自己的init进程(PID=1) |
| **Network** | 网络栈 | 2.6 | 独立的IP、端口、路由 |
| **Mount** | 文件系统挂载点 | 2.4 | 独立的文件系统视图 |
| **UTS** | 主机名和域名 | 2.6 | 独立的主机名标识 |
| **IPC** | 进程间通信 | 2.6 | 独立的信号量、消息队列 |
| **User** | 用户和用户组ID | 3.8 | 容器内可拥有root权限 |
| **Cgroup** | Cgroup根目录 | 4.6 | 新增，容器隔离cgroup视图 |

### Namespace API

| API | 功能 | 参数 |
|-----|------|------|
| `clone()` | 创建新进程并指定namespace | CLONE_NEWIPC, CLONE_NEWNET, CLONE_NEWPID 等 |
| `setns()` | 加入已有namespace | 通过 fd 指定 |
| `unshare()` | 将进程移出当前namespace | 指定隔离类型 |

### 查看 Namespace

```bash
# 查看进程所属namespace
ls -la /proc/$$/ns/

# 输出格式: xxx:[inode number]
# 如: user:[4026531837]
```

**关键特性**：
- 如果两个进程的 namespace 文件指向同一链接文件，说明在同一 namespace
- 打开链接文件可保持 namespace 存活（即使所有进程已退出）

---

## 二、Cgroups 概念

**Cgroups (Control Groups)** 是 Linux 内核限制、记录、隔离进程组物理资源的机制。

### 核心术语

| 术语 | 定义 |
|------|------|
| task | 进程或线程 |
| control group | 按标准划分的进程组 |
| hierarchy | cgroup 的树形结构，子节点继承父节点属性 |
| subsystem | 资源控制器，如 cpu、memory |

### Cgroups 功能

| 功能 | 说明 | 示例 |
|------|------|------|
| **资源限制** | 设定资源上限 | memory子系统限制内存使用 |
| **优先级控制** | 分配特定资源份额 | cpu子系统分配cpu share |
| **资源统计** | 记录使用情况 | cpuacct记录CPU时间 |
| **进程隔离** | 不同namespace隔离 | ns子系统 |
| **进程控制** | 挂起/恢复进程 | freezer子系统 |

### Cgroups 子系统

| 子系统 | 控制资源 | 说明 |
|--------|----------|------|
| **blkio** | 块设备IO | 限制磁盘读写速率 |
| **cpu** | CPU时间 | 调度器分配CPU份额 |
| **cpuacct** | CPU统计 | 自动生成CPU使用报告 |
| **cpuset** | CPU核心绑定 | 分配特定CPU和内存节点 |
| **devices** | 设备访问 | 允许/拒绝设备访问 |
| **freezer** | 进程冻结 | 挂起/恢复进程组 |
| **memory** | 内存限制 | 设定内存上限，触发OOM |
| **net_cls** | 网络标记 | 标记数据包，配合tc使用 |
| **net_prio** | 网络优先级 | 设置网络流量优先级 |
| **hugetlb** | 大页内存 | 限制HugeTLB使用 |

---

## 三、使用示例

### Memory 子系统

```bash
# 进入memory子系统目录
cd /sys/fs/cgroup/memory

# 创建控制组
mkdir test

# 设置内存上限（100MB）
echo 104857600 > test/memory.limit_in_bytes

# 将进程加入控制组
echo $$ > test/tasks
```

### CPU 子系统

```bash
# 创建控制组
mkdir /sys/fs/cgroup/cpu/hello

# 设置CPU使用上限（50%）
echo 50000 > /sys/fs/cgroup/cpu/hello/cpu.cfs_quota_us
# cpu.cfs_period_us 默认 100000（100ms周期）

# 将进程加入控制组
echo $PID > /sys/fs/cgroup/cpu/hello/tasks
```

---

## 四、与容器技术的关系

| 技术 | 作用 | 关系 |
|------|------|------|
| Namespace | 资源隔离 | 容器"看见什么" |
| Cgroups | 资源限制 | 容器"能用多少" |
| [[concepts/containerd]] | 容器运行时 | 使用 Namespace+Cgroups 实现 |
| [[concepts/oci]] | 容器标准 | 定义隔离和限制规范 |

**容器 = Namespace(隔离视图) + Cgroups(限制资源) + UnionFS(文件系统)**

---

## 五、与系统知识关联

| 系统模块 | 关联点 |
|----------|--------|
| [[summaries/linux-scheduler]] | cpu子系统使用CFS调度 |
| [[summaries/linux-meminfo]] | memory子系统触发OOM |
| [[summaries/perf-tool]] | 可分析cgroup内进程性能 |
| [[concepts/cfs-scheduler]] | cpu.cfs_quota_us 基于CFS |

---

## 相关链接

- [[concepts/namespace]] - Namespace 核心概念
- [[concepts/cgroups]] - Cgroups 核心概念
- [[summaries/containerd-runtime]] - 容器运行时实现
- [[concepts/cri]] - Kubernetes容器运行时接口