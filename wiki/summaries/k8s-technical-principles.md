---
title: Kubernetes 技术原理深入解析
created: 2026-05-16
updated: 2026-05-16
tags: [Kubernetes, K8s, 云原生, 架构原理, 调谐循环]
source_dir: Kunbernetes和Docker
source_files: [K8s云原生-阿里云-K8S技术原理.md]
---

# Kubernetes 技术原理深入解析

从设计哲学到架构原理，系统性解析 K8S 云原生"自动化帝国"。

---

## 核心设计哲学：声明式 API

### 命令式 vs 声明式

| 管理方式 | 特点 | 问题 |
|----------|------|------|
| **命令式** | 手动执行操作 | 需持续干预、无法自愈 |
| **声明式** | 描述期望状态 | 系统自动调谐、自愈扩容 |

### 调谐循环 (Reconciliation Loop)

K8S 核心驱动力，永不停止的闭环：

1. **Observe** - 读取期望状态（YAML蓝图）
2. **Compare** - 对比实际状态
3. **Act** - 发现不一致则行动
4. **Repeat** - 永不停止重复

**所有自愈、扩容、滚动更新都源于此循环。**

---

## 基础设施：Linux 内核魔法

### Namespaces - "VR眼镜"隔离

| Namespace | 隔离内容 |
|-----------|----------|
| **PID** | 进程列表（容器内PID=1） |
| **NET** | 网络端口（独占80端口） |
| **MNT** | 文件系统视图 |
| **IPC** | 信号量/消息队列 |
| **UTS** | 主机名 |

### Cgroups - "资源配给"限制

- 控制 CPU/Memory/IO 配额
- YAML `resources.limits` 的底层实现
- 限制：`cpu: "1", memory: "2Gi"`

---

## 核心架构

### 控制平面 (Control Plane) - 总部决策层

| 组件 | 职责 | 比喻 |
|------|------|------|
| **etcd** | 唯一事实来源，存储期望+实际状态 | 中央账本 |
| **kube-apiserver** | 认证/授权/准入，唯一入口 | 总机前台 |
| **kube-scheduler** | 为新Pod找最合适的Node | 排班经理 |
| **kube-controller-manager** | 调谐循环执行者，各种控制器 | 监工部门 |

### 数据平面 (Worker Node) - 分店执行层

| 组件 | 职责 | 比喻 |
|------|------|------|
| **kubelet** | 监控账本，执行本店任务 | 分店经理 |
| **containerd** | 运行容器 | 厨房员工 |
| **kube-proxy** | 服务发现/负载均衡 | 智能服务员 |

---

## 核心抽象概念

| 资源 | 说明 |
|------|------|
| **Pod** | 部署最小单元，可含多个容器，共享网络命名空间 |
| **Deployment** | 无状态应用声明，创建ReplicaSet |
| **Service** | 稳定虚拟IP，负载均衡到Pod |
| **ConfigMap/Secret** | 配置与机密外挂 |
| **PV/PVC** | 持久化存储声明与绑定 |

---

## 网络原理

### K8S 网络三铁律

1. 所有 Pod 有唯一集群可见IP
2. Pod-to-Pod 无 NAT 直通
3. Node-to-Pod 无 NAT 直通

### CNI 插件流派

| 模式 | 实现 | 特点 |
|------|------|------|
| **Overlay (VXLAN)** | Flannel | "包中包"封装，简单但有开销 |
| **BGP路由** | Calico | 无封装，性能高，支持NetworkPolicy |

### kube-proxy 实现

| 模式 | 时间复杂度 | 适用场景 |
|------|------------|----------|
| **iptables** | O(n) | 小集群 |
| **IPVS** | O(1) | 大集群标配，哈希查找 |

---

## 高级生态

### Ingress - 南北向流量入口

- L7(HTTP/HTTPS) 统一入口
- Ingress Controller（Nginx/Traefik）实现规则
- 域名路由到内部 Service

### Service Mesh (Istio) - 东西向流量安全

- Sidecar 注入：每个Pod自动加 Envoy
- 流量劫持：iptables 重定向
- mTLS：自动双向加密
- 熔断/重试/超时：自动韧性

### GitOps (ArgoCD) - 终极运维

- Git 为唯一事实来源
- Pull模式：自动同步期望状态
- 杜绝漂移、完美审计、一键回滚

### Operator 模式 - 外聘专家

- **CRD**：自定义资源定义（新词汇）
- **Custom Controller**：编码专家知识
- Day2运维：主从切换、滚动升级

---

## 可观测性三叉戟

| 类型 | 工具 | 原理 |
|------|------|------|
| **Metrics** | Prometheus | Pull模型 + K8S服务发现 |
| **Logging** | Loki/EFK | DaemonSet采集 + 标签索引 |
| **Tracing** | Jaeger | TraceID传递 + Span拼装 |

---

## 终极形态

### Serverless (Knative)

- Scale-to-Zero：无流量自动缩零
- 冷启动：Activator截住请求 → 启Pod → 松开
- 函数即服务

### 虚机兼容 (KubeVirt)

- VMI YAML → 创建Pod运行QEMU
- "套娃"架构：Pod内跑完整虚拟机
- 统一管理：容器+虚拟机共享CNI/CSI

---

## 相关链接

- [[summaries/kubernetes-core-concepts]] - K8s核心概念
- [[summaries/k8s-architecture-official]] - K8s官方架构
- [[summaries/containerd-runtime]] - 容器运行时
- [[concepts/kubernetes]] - Kubernetes概念
- [[concepts/namespace]] - Namespace隔离
- [[concepts/cgroups]] - Cgroups限制