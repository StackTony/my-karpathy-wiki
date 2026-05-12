---
title: OCI (Open Container Initiative)
created: 2026-05-12
updated: 2026-05-12
tags: [OCI, 容器标准, runc]
---

# OCI 开放容器标准

容器运行时的标准化规范，规定镜像结构和运行时接口。

## 背景

Docker 将 libcontainer 捐献改名为 runc，成为 OCI 参考实现。

## 标准组成

| 标准 | 内容 |
|------|------|
| OCI Image Spec | 镜像结构规范 |
| OCI Runtime Spec | 运行时接口规范 |

## 运行时接口

OCI Runtime Spec 定义容器操作命令：

| 命令 | 说明 |
|------|------|
| create | 创建容器 |
| start | 启动容器 |
| kill | 终止容器 |
| delete | 删除容器 |
| state | 查询状态 |

## OCI 实现

| 实现 | 说明 |
|------|------|
| runc | Docker 捐献的参考实现 |
| Kata Containers | 安全容器 |
| gVisor | Google 沙箱容器 |

## runc 特点

- 纯执行工具，无镜像管理
- 无网络配置，只负责"跑容器"
- 启动后退出，父进程为 containerd-shim

## 与虚拟化类比

| 执行器 | 容器 | 虚拟化 |
|--------|------|--------|
| runc | 纯执行工具 | 全功能模拟器 |
| QEMU | - | 硬件模拟+设备管理 |

## 相关链接

- [[summaries/containerd-runtime]]
- [[concepts/containerd]]
- [[concepts/cri]]