---
title: Containerd
created: 2026-05-12
updated: 2026-05-12
tags: [容器运行时, containerd, Docker]
---

# Containerd

工业级标准容器运行时，从 Docker Engine 分离出来的独立项目。

## 核心职责

- 容器生命周期管理（创建到销毁）
- 镜像拉取/推送
- 存储管理
- 调用 runc 运行容器
- 网络接口管理

## 设计特点

| 特性 | 说明 |
|------|------|
| 简单性 | 仅容器运行，不含编排 |
| 健壮性 | containerd-shim 垫片隔离 |
| 可移植性 | Linux/Windows 支持 |

## 与 Docker 关系

```
Docker Daemon → containerd → containerd-shim → runc
```

Docker 公司将 containerd 捐献给 CNCF 基金会。

## 与虚拟化类比

| 层次 | 容器 | 虚拟化 |
|------|------|--------|
| 编排 | Kubernetes | Nova |
| 管理 | containerd | libvirtd |
| 执行 | runc | QEMU |

## 相关链接

- [[summaries/containerd-runtime]]
- [[concepts/cri]]
- [[concepts/oci]]