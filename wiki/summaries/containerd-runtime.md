---
title: Containerd 容器运行时
created: 2026-05-12
updated: 2026-05-12
tags: [容器, containerd, docker, Kubernetes, CRI, OCI]
sources: [Kunbernetes和Docker]
---

# Containerd 容器运行时

Containerd 是工业级标准的容器运行时，负责容器生命周期管理、镜像管理、存储和网络。

## 架构类比

| 组件 | 虚拟化类比 | 说明 |
|------|------------|------|
| Kubernetes | Nova | 编排层 |
| containerd | libvirtd | 中间管理层 |
| runc | QEMU | 底层执行器 |

**关键差异**：
- 容器：操作系统级隔离（namespace/cgroups），共享内核，轻量
- 虚拟机：硬件级隔离，独立内核，重量

## Docker 架构演进

```
Docker 1.11+ 架构:
Docker Client
    ↓
Docker Daemon (gRPC)
    ↓
containerd
    ↓
containerd-shim (垫片进程)
    ↓
runc (OCI实现) → 启动后退出
```

**containerd-shim 作用**：
- 成为容器进程父进程
- 收集容器状态上报 containerd
- 清理子进程避免僵尸进程
- containerd 挂掉不影响容器

## CRI 容器运行时接口

Kubernetes 定义的标准接口，解耦 kubelet 与容器运行时。

```
CRI API:
├── ImageService → 镜像操作
└── RuntimeService → Pod/容器生命周期
```

### dockershim 方案（已废弃）

```
kubelet → dockershim (内置) → Docker Daemon → containerd → runc
```

调用链过长，Kubernetes 1.24 已移除内置 dockershim。

### containerd 方案

```
kubelet → containerd (内置CRI插件) → containerd-shim → runc
```

Containerd 1.1+ 内置 CRI 实现，调用链更简洁。

### CRI-O 方案

```
kubelet → CRI-O → runc
```

Kubernetes 专用 CRI 运行时，最简洁。

## Containerd 架构

```
Containerd 三大模块:
├── Storage → 镜像/容器数据存储
├── Metadata → 元数据管理 (bolt数据库)
└── Runtime → 容器执行 (调用runc)
```

### 插件类型

| 插件 | 功能 |
|------|------|
| Content Plugin | 镜像可寻址内容存储 |
| Snapshot Plugin | 文件系统快照管理 |
| Metadata Plugin | 容器/镜像元数据 |
| Runtime Plugin | 容器执行 |

### 存储路径

| 路径 | 说明 |
|------|------|
| `/var/lib/containerd` | 持久化数据 |
| `/run/containerd` | 运行时临时数据 |

## ctr 命令行工具

### 镜像操作

```bash
# 拉取镜像
ctr image pull docker.io/library/nginx:alpine

# 列出镜像
ctr image ls -q

# 打标签
ctr image tag docker.io/library/nginx:alpine harbor.local/nginx:alpine

# 导出/导入
ctr image export nginx.tar.gz docker.io/library/nginx:alpine
ctr image import nginx.tar.gz
```

### 容器操作

```bash
# 创建容器（静态）
ctr container create docker.io/library/nginx:alpine nginx

# 启动任务（运行）
ctr task start -d nginx

# 进入容器
ctr task exec --exec-id 0 -t nginx sh

# 查看状态
ctr task ls
ctr task metrics nginx
```

### 命名空间

```bash
# 查看命名空间
ctr ns ls

# Docker 使用 moby 命名空间
ctr -n moby container ls

# Kubernetes 使用 k8s.io 命名空间
ctr -n k8s.io container ls
```

## systemd 配置要点

| 参数 | 说明 |
|------|------|
| Delegate=yes | containerd 管理 cgroups |
| KillMode=process | 重启不杀子进程 |

## 相关链接

- [[concepts/containerd]]
- [[concepts/cri]]
- [[concepts/oci]]