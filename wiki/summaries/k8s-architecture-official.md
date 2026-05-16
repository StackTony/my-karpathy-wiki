---
title: Kubernetes 集群架构（官方文档）
created: 2026-05-14
updated: 2026-05-14
tags: [Kubernetes, K8s, 架构, 官方文档]
credibility: low
source_dir: Self learn
source_files: [K8s云原生-官方文档-K8s架构.md]
---

# Kubernetes 集群架构（官方文档）

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

Kubernetes 集群由控制平面和一组工作节点组成，用于运行容器化应用。

---

## 架构组成

![控制平面（kube-apiserver、etcd、kube-controller-manager、kube-scheduler）和多个节点](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)

---

## 控制平面组件

| 组件 | 作用 |
|------|------|
| **kube-apiserver** | 公开Kubernetes API，处理请求，控制平面前端 |
| **etcd** | 一致且高可用的KV存储，作为集群后台数据库 |
| **kube-scheduler** | 监视未指定运行节点的Pod，选择节点运行 |
| **kube-controller-manager** | 运行控制器进程（Node/Job/EndpointSlice/ServiceAccount） |
| **cloud-controller-manager** | 嵌入云平台控制逻辑，连接云提供商API |

### kube-apiserver

设计上支持**水平扩缩**：部署多个实例，负载均衡。

### kube-scheduler

调度决策考虑因素：
- 单Pod及多Pod集合的资源需求
- 软硬件及策略约束
- 亲和性及反亲和性规范
- 数据位置、工作负载干扰

### kube-controller-manager 控制器类型

- **Node控制器**：节点故障通知和响应
- **Job控制器**：监测一次性任务，创建Pod运行
- **EndpointSlice控制器**：填充Service和Pod链接
- **ServiceAccount控制器**：新命名空间创建默认ServiceAccount

---

## 节点组件

| 组件 | 作用 |
|------|------|
| **kubelet** | 保证容器运行在Pod中，接收PodSpec确保容器运行健康 |
| **kube-proxy**（可选） | 网络代理，维护网络规则，实现Service概念 |
| **容器运行时** | 管理容器执行和生命周期（containerd、CRI-O等） |

### kube-proxy

- 维护节点网络规则
- 允许集群内部/外部与Pod通信
- 使用OS数据包过滤层实现规则（否则仅转发）

---

## 插件（Addons）

| 插件 | 作用 |
|------|------|
| **DNS** | 集群DNS服务器，为Kubernetes服务提供DNS记录（几乎必需） |
| **Dashboard** | Web UI，管理应用和集群 |
| **容器资源监控** | 时序度量值保存到集中数据库 |
| **集群层面日志** | 容器日志保存到集中存储 |
| **网络插件** | CNI实现，分配IP、使Pod能通信 |

---

## 架构变种

### 控制平面部署选项

| 方式 | 说明 |
|------|------|
| **传统部署** | 直接在专用机器/VM运行，systemd服务管理 |
| **静态Pod** | kubeadm常用方法，由kubelet管理 |
| **自托管** | 在集群内作为Pod运行 |
| **托管服务** | 云平台抽象控制平面 |

### 定制和可扩展性

- 自定义调度器（协同或替换默认）
- API服务器扩展（CRD、API聚合）
- 云平台深度集成（cloud-controller-manager）

---

## 相关链接

- [[summaries/kubernetes-core-concepts]] - K8s核心概念与架构
- [[summaries/containerd-runtime]] - Containerd容器运行时
- [[concepts/cri]] - CRI接口
- [[concepts/namespace]] - 资源隔离
- [[concepts/cgroups]] - 资源限制