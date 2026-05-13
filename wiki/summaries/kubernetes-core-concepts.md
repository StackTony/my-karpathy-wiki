---
title: Kubernetes 核心概念与架构
created: 2026-05-13
updated: 2026-05-13
tags: [Kubernetes, K8s, 容器编排, Pod, Service, Deployment]
source_dir: Kunbernetes和Docker
source_files: [Kubernetes（K8s）全面解析：核心概念、架构与实践.md]
---

# Kubernetes 核心概念与架构

Kubernetes（K8s）是 Google 开源的容器编排平台，自动化容器的部署、扩展、运维和管理，是云原生生态的核心技术。

---

## 一、K8s 是什么？

| 概念 | 说明 |
|------|------|
| 名称来源 | 希腊语"舵手"或"飞行员" |
| 核心目标 | 让容器化应用在集群中高效、可靠地运行 |
| 类比 | Docker是"集装箱"，K8s是"港口调度系统" |

---

## 二、核心优势

| 优势 | 说明 |
|------|------|
| 自动化运维 | 自动部署、重启、扩缩容 |
| 高可用性 | 故障自动调度，服务不中断 |
| 弹性伸缩 | 基于CPU/QPS自动增减容器 |
| 负载均衡 | 内置服务发现和流量分发 |
| 滚动更新 | 无停机更新，失败可回滚 |
| 跨环境兼容 | 公有云、私有云、混合云统一 |

---

## 三、核心概念

### 1. Pod

| 特性 | 说明 |
|------|------|
| 定义 | 最小部署单元，一个或多个容器组合 |
| 网络 | 共享Pod的IP和端口空间（localhost通信） |
| 存储 | 共享存储卷 |
| 生命周期 | 短暂，需控制器管理 |
| 基础设施 | Pause容器维持网络命名空间 |

**示例**：Web应用Pod = 应用容器 + 日志收集容器

### 2. 控制器（Controller）

| 控制器 | 用途 |
|--------|------|
| **Deployment** | 无状态应用，滚动更新、扩缩容、回滚 |
| **StatefulSet** | 有状态应用（数据库），唯一网络标识和存储 |
| **DaemonSet** | 每节点运行一个Pod副本（监控/日志Agent） |
| **Job/CronJob** | 一次性任务 / 定时任务 |

### 3. Service

解决 Pod 动态 IP 变化问题，提供稳定的网络访问入口。

| 类型 | 说明 |
|------|------|
| **ClusterIP** | 默认，仅集群内部访问 |
| **NodePort** | 节点开放静态端口，外部可访问 |
| **LoadBalancer** | 云服务商负载均衡器（公有云） |
| **Ingress** | HTTP/HTTPS域名路由（需Ingress Controller） |

### 4. 命名空间（Namespace）

| 命名空间 | 说明 |
|----------|------|
| `default` | 未指定时的默认位置 |
| `kube-system` | K8s系统组件 |
| `kube-public` | 公共资源（所有用户可读） |

### 5. 配置与存储

| 资源 | 用途 |
|------|------|
| **ConfigMap** | 非敏感配置（环境变量、配置文件） |
| **Secret** | 敏感数据（密码、Token），Base64编码 |
| **Volume** | Pod持久化存储 |
| **PV/PVC** | 集群存储资源 / Pod存储申请 |

### 6. 标签与选择器

```yaml
labels:
  app: nginx
  env: prod
selector:
  matchLabels:
    app: nginx
```

---

## 四、架构设计

### Master-Node 架构

```
┌─────────────────────────────────────────────────────┐
│                 控制平面（Master）                     │
│  ┌──────────────┐  ┌──────────────┐                 │
│  │ API Server   │  │ Scheduler    │                 │
│  │ (统一入口)   │  │ (Pod调度)    │                 │
│  └──────┬───────┘  └──────────────┘                 │
│         │                                           │
│  ┌──────▼───────┐  ┌──────────────┐                 │
│  │    etcd      │  │ Controller   │                 │
│  │ (状态存储)   │  │ Manager      │                 │
│  └──────────────┘  └──────────────┘                 │
└─────────────────────────────────────────────────────┘
         │
         │ API通信
         │
┌─────────────────────────────────────────────────────┐
│                   工作节点（Node）                     │
│  ┌──────────────┐  ┌──────────────┐                 │
│  │   kubelet    │  │ kube-proxy   │                 │
│  │ (Pod管理)    │  │ (网络规则)   │                 │
│  └──────┬───────┘  └──────────────┘                 │
│         │                                           │
│  ┌──────▼───────┐                                   │
│  │ 容器运行时    │  containerd / CRI-O              │
│  │ + CNI网络    │  Flannel/Calico/Cilium            │
│  └──────────────┘                                   │
└─────────────────────────────────────────────────────┘
```

### 控制平面组件

| 组件 | 功能 |
|------|------|
| **kube-apiserver** | 集群统一入口，RESTful API |
| **etcd** | 集群状态数据库（高可用关键） |
| **kube-scheduler** | Pod调度，基于资源需求和亲和性 |
| **kube-controller-manager** | 多种控制器，维护集群状态 |

### 工作节点组件

| 组件 | 功能 |
|------|------|
| **kubelet** | Node代理，管理Pod生命周期 |
| **kube-proxy** | 维护网络规则，Service负载均衡 |
| **容器运行时** | containerd、CRI-O（通过CRI接口） |
| **CNI** | Pod网络通信（Flannel/Calico/Cilium） |

---

## 五、核心命令

### 集群信息

```bash
kubectl get nodes          # 查看节点
kubectl cluster-info       # 集群信息
kubectl get namespaces     # 命名空间
```

### 资源操作

```bash
kubectl apply -f xxx.yaml        # 创建资源
kubectl get deployments/pods     # 查看资源
kubectl describe pod <name>      # 详情
kubectl logs <pod>               # 日志
kubectl exec -it <pod> -- bash   # 进入容器
kubectl scale deployment xxx --replicas=5  # 扩缩容
kubectl delete deployment xxx    # 删除
```

### 配置操作

```bash
kubectl create configmap xxx --from-literal=key=value
kubectl create secret generic xxx --from-literal=password=xxx
kubectl get configmaps/secrets
```

---

## 六、YAML 资源清单

### Deployment 示例

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: "1"
            memory: "1Gi"
          requests:
            cpu: "0.5"
            memory: "512Mi"
```

### Service 示例

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

---

## 七、核心功能实践

### 自动扩缩容（HPA）

```bash
kubectl autoscale deployment nginx --min=2 --max=10 --cpu-percent=50
```

### 滚动更新与回滚

```bash
kubectl set image deployment/nginx nginx=nginx:1.26
kubectl rollout status deployment/nginx
kubectl rollout undo deployment/nginx
```

### 数据持久化（PV/PVC）

```yaml
# PV
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nginx-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/nginx-pv

# PVC
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

---

## 八、生态工具

| 类别 | 工具 |
|------|------|
| 监控 | Prometheus + Grafana |
| 日志 | ELK Stack、Loki |
| 服务网格 | Istio、Linkerd |
| CI/CD | Jenkins、GitLab CI、ArgoCD |
| 镜像仓库 | Harbor、Docker Hub |
| 管理平台 | KubeSphere、Rancher |

---

## 九、与现有知识关联

| 系统模块 | 关联点 |
|----------|--------|
| [[summaries/containerd-runtime]] | K8s使用containerd作为容器运行时 |
| [[concepts/cri]] | K8s通过CRI接口调用运行时 |
| [[concepts/namespace]] | K8s使用Linux Namespace隔离 |
| [[concepts/cgroups]] | K8s使用Cgroups限制资源 |

---

## 相关链接

- [[summaries/containerd-runtime]] - 容器运行时
- [[concepts/cri]] - CRI接口
- [[concepts/oci]] - OCI标准
- [[concepts/namespace]] - 资源隔离
- [[concepts/cgroups]] - 资源限制