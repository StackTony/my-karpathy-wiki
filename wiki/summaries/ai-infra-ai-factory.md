---
title: AI Factory 五阶段架构演进
created: 2026-05-14
updated: 2026-05-14
tags: [AI基础设施, AI Factory, GPU集群, MLOps]
credibility: low
source_dir: Self learn
source_files: [AI infra-vCluster-AI Factory架构.md]
---

# AI Factory 五阶段架构演进

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

AI Factory 是端到端自动化平台，将原始数据转化为生产就绪的 ML 模型——类似现代制造线。

---

## AI Factory 定义

**不是单一产品**，而是**开放参考架构**：
- Nvidia、Mirantis 等提供验证设计蓝图
- 标准化节点配置、Fabric选择、企业AI软件栈
- 降低部署风险，加速实施

---

## 五阶段演进路径

### Stage 1: Basic GPU Cluster

少量GPU节点，手动管理（如 `kubectl apply`）。

**何时升级**：频繁资源冲突、多用户、手动调度耗时。

### Stage 2: Managed Workloads and Monitoring

引入 Nvidia GPU Operator、Prometheus 观测、基本作业队列。

**何时升级**：多团队竞争优先级、跨团队资源冲突、合规要求更强隔离。

### Stage 3: Multi-Tenancy and Access Control

**Namespace局限**：共享集群级资源（CRD冲突）、共享kubeconfig安全隐患。

**vCluster 解决方案**：
- 每团队独立虚拟控制平面
- 独立API资源、无CRD版本冲突
- 自定义调度器、自定义准入控制器

**何时升级**：开发者生产力受限、频繁请求自定义环境。

### Stage 4: Platformization

JupyterHub notebooks、生产ML pipelines、GitOps/CICD集成。

**自服务关键**：vCluster 自动创建和管理虚拟集群。

**何时升级**：模型数量、合规需求、业务单元扩张使手动审批无法持续。

### Stage 5: AI Factory

**全自动化闭环**：
- Git代码→触发预处理→训练→测试→部署
- Policy-as-code 安全/隐私/预算验证
- 使用遥测→实时quota→billing→自动限流
- 签名registry记录数据/代码/参数→完整血缘图

---

## 核心组件

| 组件 | 作用 |
|------|------|
| **Dynamic GPU Infrastructure** | MIG分区、按团队/工作负载隔离 |
| **Kubernetes + vCluster** | 控制平面隔离、GPU共享安全 |
| **ML Workflow Orchestration** | Kubeflow/MLflow/Argo Workflows |
| **Developer Interfaces** | JupyterHub、VS Code Server |
| **Observability** | DCGM + Prometheus GPU指标 |
| **Security** | Pod Security、Network Policy |

---

## GPU集群挑战 vs AI Factory收益

| 挑战 | 解决方案 |
|------|----------|
| 手动移交 | 自服务基础设施 |
| 慢onboarding | 标准化接口+自动化 |
| 拓扑无感知调度 | 拓扑感知智能调度 |
| 资源竞争 | 环境隔离 |
| 低GPU利用率 | 高级调度匹配GPU/CPU/Memory/Network |
| 不可预测成本 | 使用计量+chargeback |

---

## 相关链接

### AI infra 系列关联

- [[summaries/ai-infra-gpu-fabric]] - GPU Fabric 设计（网络是系统核心）
- [[summaries/ai-infra-compute-design]] - AI 计算基础设施五层架构（存储/RDMA设计）

### 其他关联

- [[summaries/kubernetes-core-concepts]] - K8s编排基础
- [[concepts/namespace]] - 资源隔离
- [[concepts/cgroups]] - 资源限制