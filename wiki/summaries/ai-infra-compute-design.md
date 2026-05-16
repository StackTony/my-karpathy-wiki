---
title: AI 计算基础设施五层架构设计
created: 2026-05-14
updated: 2026-05-14
tags: [AI基础设施, 存储, RDMA, GPU]
credibility: low
source_dir: Self learn
source_files: [AI infra-Scality-计算基础设施设计.md]
---

# AI 计算基础设施五层架构设计

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

AI 计算基础设施与传统数据中心基础设施根本不同：GPU为中心、数据量快速增长、IO突发并行。

---

## 五层架构

| 层级 | 作用 | 关键指标 |
|------|------|----------|
| **GPU Clusters** | 并行矩阵计算 | GPU利用率%、内存带宽 |
| **High-speed Networking/RDMA** | 低延迟数据移动 | 延迟(µs)、带宽(Tb/s) |
| **Storage Tiers** | 训练数据、checkpoint、缓存 | 吞吐(GB/s)、IOPS、容量 |
| **Orchestration** | GPU作业调度 | 作业队列深度、调度开销 |
| **Monitoring** | GPU饱和、存储IO、作业进度 | 端到端管道可见性 |

---

## RDMA：消除CPU瓶颈

**Remote Direct Memory Access** 让存储系统直接向GPU输送数据，无需CPU参与。

| 网络 | 特点 |
|------|------|
| TCP/IP | 引入延迟和CPU开销，吞吐天花板 |
| InfiniBand/RoCEv2 + RDMA | 消除开销，GPU-direct存储访问 |

**三个平面**：
1. GPU-to-GPU（AllReduce集体操作）
2. GPU-to-storage（数据加载、checkpoint写入）
3. CPU-to-orchestration（作业调度）

---

## 四级存储设计

| 级别 | 存储 | 延迟目标 | 用途 |
|------|------|----------|------|
| **GPU-Direct** | TLC flash + S3 over RDMA | <50µs | 实时训练、KV cache、checkpoint |
| **Hot** | QLC/NL-SSD | 多TB/s吞吐 | 活跃训练集、embeddings |
| **Warm** | 混合存储 | 中等 | 分阶段数据、RAG索引 |
| **Cold** | 高密度容量 | 低优先级 | 原始数据归档、模型血缘 |

---

## 设计原则

### Disaggregation

**计算和存储分离**，各自独立扩展：
- 不买存储也能加GPU节点
- 不加GPU也能扩展存储容量

### Scale-out over Scale-up

水平扩展优于垂直扩展：
- 分布式对象存储提供线性吞吐增长
- 单一大型NAS头单元成为吞吐瓶颈

### Workload-aligned Tiers

不同AI工作负载有不同存储需求：
- Checkpoint IO：高写入吞吐、低延迟
- Dataset ingestion：持续高读取带宽
- RAG retrieval：快速随机读取
- Model archival：密度和持久性

---

## AI数据生命周期存储需求

| 阶段 | 存储要求 |
|------|----------|
| **Ingest & Preparation** | 高写入带宽、大容量 |
| **Training Datasets** | 高聚合吞吐、并行读取 |
| **Checkpoints** | 突发高带宽、低延迟 |
| **Inference Serving** | 快加载、KV cache超低延迟随机读 |
| **Retention & Archival** | 高密度、强持久性、生命周期自动化 |

---

## 相关链接

### AI infra 系列关联

- [[summaries/ai-infra-gpu-fabric]] - GPU Fabric 设计（P99延迟约束、RDMA网络）
- [[summaries/ai-infra-ai-factory]] - AI Factory 五阶段演进（从GPU集群到自动化产线）

### 其他关联

- [[summaries/linux-io-mechanism]] - Linux IO栈架构
- [[concepts/kvm-virtualization]] - 虚拟化性能指标