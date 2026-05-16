---
title: AI 基础设施架构 - GPU Fabric 设计
created: 2026-05-14
updated: 2026-05-14
tags: [AI基础设施, GPU, Fabric, 网络]
credibility: low
source_dir: Self learn
source_files: [AI infra-Rack2Cloud-GPU Fabric架构.md]
---

# AI 基础设施架构 - GPU Fabric 设计

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

AI 基础设施不是软件问题的硬件附录，而是基础设施决策——在部署前决定，一旦架构设定则难以逆转。

---

## 核心原则

### The Fabric Is the System

在分布式训练集群中，网络不是支持计算的设施——**网络就是系统**。

GPU 计算没有确定性 Fabric 不是训练集群——只是等待网络解决争用事件的昂贵硬件集合。

**P99 延迟是分布式训练性能的真正约束**：
- 511 个 GPU 在 10ms 完成
- 1 个 GPU 因拥塞等待 15ms
- 整个训练步骤等待所有 512 个节点同步

---

## 控制平面 vs 数据平面

| 层面 | 职责 | 组成 |
|------|------|------|
| **控制平面** | 决策 | Scheduler、Model Routing、Policy、Cost Attribution |
| **数据平面** | 执行 | GPU/TPU、NVLink/RDMA、Token Generation、Checkpoint I/O |

**大多数失败发生在两者边界**：
- 控制平面失败 = 配置失败（调度设置、执行预算、路由策略）
- 数据平面失败 = 物理失败（Fabric P99 违规、Checkpoint 吞吐瓶颈、内存带宽耗尽）

---

## Training vs Inference：两种不同的基础设施问题

| 特性 | Training | Inference |
|------|----------|-----------|
| **成本模型** | 有界资本事件 | 连续运营支出 |
| **物理约束** | 梯度同步、Fabric带宽 | Token经济学、延迟/Watt |
| **主要失败模式** | Fabric P99→梯度停滞 | 行为驱动成本漂移→无声账单累积 |
| **存储** | Checkpoint吞吐关键 | KV-cache实时访问 |

**GTC 2026 硬件分离**：NVIDIA 首次推出专用推理硅（Groq 3 LPX rack），Training/Inference 分离成为采购决策。

---

## 云 vs On-Prem 决策框架

| 场景 | 建议 | 主要风险 |
|------|------|----------|
| 早期AI，需求不可预测 | 云优先 | 无运行时控制的推理成本漂移 |
| 生产推理，稳定需求 | 优化+混合 | 专用硬件GPU利用率不足 |
| 高规模推理，GPU util >60% | On-Prem GPU集群 | CapEx锁定 |
| RAG重应用 | 计算+向量DB共置 | 分离导致检索延迟和出口成本 |

**盈亏平衡阈值**：约 60-70% 稳定 GPU 利用率，12-18 月周期。

---

## Sovereign AI

主权AI不仅是模型在哪里运行——**是控制平面能否被外部触及**。

四个要求：
1. 本地控制平面（独立于外部身份提供商）
2. GPU集群治理（无需云控制台访问）
3. 数据驻留强制（存储和检索层）
4. 推理服务（留在主权边界内）

---

## 关键指标

| 指标 | 目标值 | 说明 |
|------|--------|------|
| P99 延迟 | <300ms | 交互式推理UX临界点 |
| GPU利用率 | 60%+ | On-Prem 经济性超越云的阈值 |
| Checkpoint吞吐 | 5-20GB/s | 分布式训练底层，低于此GPU空闲 |
| 推理吞吐/Watt | 10x | Vera Rubin NVL72 vs Blackwell |

---

## 相关链接

### AI infra 系列关联

- [[summaries/ai-infra-ai-factory]] - AI Factory 五阶段演进（从GPU集群到自动化产线）
- [[summaries/ai-infra-compute-design]] - AI 计算基础设施五层架构（存储/RDMA设计）

### 其他关联

- [[summaries/kubernetes-core-concepts]] - K8s编排层
- [[concepts/kvm-virtualization]] - 虚拟化性能指标
- [[summaries/linux-io-mechanism]] - IO栈吞吐
- [[concepts/namespace]] + [[concepts/cgroups]] - 底层隔离技术