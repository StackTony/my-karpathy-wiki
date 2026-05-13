---
title: Kubernetes
created: 2026-05-13
updated: 2026-05-13
tags: [Kubernetes, K8s, 容器编排]
---

# Kubernetes

## 定义

Google开源的容器编排平台，自动化容器部署、扩展、运维和管理。

## 核心概念

| 概念 | 说明 |
|------|------|
| Pod | 最小部署单元 |
| Deployment | 无状态应用控制器 |
| StatefulSet | 有状态应用控制器 |
| Service | 网络访问入口 |
| Namespace | 资源隔离 |

## 架构

```
Master: API Server + Scheduler + Controller + etcd
Node:   kubelet + kube-proxy + 容器运行时 + CNI
```

## 关联

- [[summaries/kubernetes-core-concepts]] - 完整概念与架构
- [[summaries/containerd-runtime]] - 容器运行时
- [[concepts/cri]] - CRI接口
- [[concepts/namespace]] - 资源隔离
- [[concepts/cgroups]] - 资源限制