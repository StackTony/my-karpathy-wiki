# Wiki Index

内容目录。每次 ingest 时自动更新。

---

## 高可信度 Summaries (✓ 已审核)

*来源：`raw/sources/` 除 `Self learn` 外的目录，内容经过用户审核确认。*

| Summary | Description | Source |
|---------|-------------|--------|
| [[summaries/linux-irq-interrupt]] | Linux IRQ中断机制，核间中断IPI | Linux操作系统/Linux中断系统 |
| [[summaries/linux-network-stack]] | Linux网络协议栈完整流程，sk_buff核心结构 | Linux操作系统/Linux网络 |
| [[summaries/linux-network-protocols]] | Linux常用网络协议：TCP/UDP/IP/ICMP/ARP/LACP | Linux操作系统/Linux网络 |
| [[summaries/linux-io-mechanism]] | Linux IO栈架构，Block层→SCSI层→驱动→硬件 | Linux操作系统/Linux IO机制 |
| [[summaries/linux-io-scheduler]] | Linux IO调度算法：noop/deadline/cfq/mq-deadline/bfq | Linux操作系统/Linux IO机制 |
| [[summaries/linux-lock-mechanisms]] | Linux内核锁机制全景对比，Spinlock/Mutex/RCU等 | Linux操作系统/Linux锁机制 |
| [[summaries/linux-meminfo]] | /proc/meminfo详解：内存统计、黑洞问题、关系式 | Linux操作系统/Linux内存管理 |
| [[summaries/linux-scheduler]] | Linux进程调度器：CFS、红黑树、vruntime机制 | Linux操作系统/Linux进程调度 |
| [[summaries/linux-namespace-cgroups]] | Namespace资源隔离+Cgroups资源限制，容器技术基石 | Linux操作系统/Linux资源隔离 |
| [[summaries/linux-boot-process]] | Linux启动详细过程：BIOS→MBR→GRUB→内核→init十步流程 | Linux操作系统/Linux系统启动关闭 |
| [[summaries/linux-shutdown-process]] | Linux关机流程深度解析：进程终止→文件系统卸载→ACPI断电 | Linux操作系统/Linux系统启动关闭 |
| [[summaries/dfx-tools-overview]] | DFX工具总览，CPU/Trace/内存/vmcore/IO/网络分析 | DFX工具 |
| [[summaries/perf-tool]] | perf性能分析工具，事件采样、火焰图、调度轨迹 | DFX工具/==CPU== |
| [[summaries/flamegraph-tool]] | 火焰图可视化分析，CPU调用栈热点定位 | DFX工具/==设置trace点== |
| [[summaries/ftrace-kprobe-tools]] | ftrace与kprobe内核追踪，函数调用链分析 | DFX工具/==设置trace点== |
| [[summaries/kvmtop-tool]] | kvmtop虚拟机监控，EXT指标、%ST抢占分析 | DFX工具/==CPU== |
| [[summaries/vmcore-analysis]] | vmcore崩溃转储解析，crash工具、寄存器分析 | DFX工具/==vmcore解析== |
| [[summaries/gdb-debugging]] | GDB调试指南，QEMU初始化调试、常用命令 | DFX工具/==gdb调试== |
| [[summaries/io-tools]] | IO性能工具：iostat/fio/dd/blktrace | DFX工具/==IO== |
| [[summaries/memory-analysis-tools]] | 内存分析：NUMA分布、slab分析、内存预占 | DFX工具/==内存== |
| [[summaries/interrupt-monitoring]] | 中断实时监控脚本，/proc/interrupts分析 | DFX工具/==中断== |
| [[summaries/network-tools]] | 网络性能工具：iperf带宽测试 | DFX工具/==网络== |
| [[summaries/tcpdump-tool]] | tcpdump网络抓包分析：过滤表达式、参数详解 | DFX工具/==网络== |
| [[summaries/virtio-architecture]] | Virtio整体架构：前后端、Vring、性能演进 | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-notification-mechanism]] | Virtio消息通知：ioeventfd+irqfd零拷贝机制 | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-vring-mechanism]] | Virtio Vring数据共享：desc/avail/used环形缓冲 | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-device-types]] | Virtio设备类型：virtio-blk/scsi/net、vDPA直通 | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-device-init]] | Virtio设备初始化流程：QEMU设备模型、realize | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-net-forwarding]] | Virtio-net内核态网络转发流程图 | Linux虚拟化/网络虚拟化 |
| [[summaries/device-passthrough]] | 设备直通：IOMMU/SR-IOV/VFIO/vDPA机制 | Linux虚拟化/IO虚拟化 |
| [[summaries/kvm-interrupt-injection]] | KVM中断注入机制：irqfd、路由表 | Linux虚拟化/中断虚拟化 |
| [[summaries/vgic-interrupt-virtualization]] | VGIC中断虚拟化：GICV2/V3、NON-VHE/VHE模式 | Linux虚拟化/中断虚拟化 |
| [[summaries/kvm-live-migration]] | KVM热迁移机制：内存拷贝三阶段、网络恢复时序 | Linux虚拟化/热迁移 |
| [[summaries/containerd-runtime]] | Containerd容器运行时：CRI/OCI、架构与ctr命令 | Kunbernetes和Docker |
| [[summaries/kubernetes-core-concepts]] | Kubernetes核心概念与架构：Pod/Service/Deployment | Kunbernetes和Docker |
| [[summaries/binary-tree-basics]] | 二叉树基础：形态、遍历、存储、高频题型 | 数据结构与算法/树 |
| [[summaries/red-black-tree-detailed]] | 红黑树详解：性质、插入删除操作、与B树等价 | 数据结构与算法/树 |
| [[summaries/avl-btree-overview]] | AVL树与B树/B+树：对比、数据库索引选择 | 数据结构与算法/树 |
| [[summaries/langchain-architecture]] | LangChain设计原理：Runnable/LCEL/Agent/RAG/LangGraph | AI人工智能/Agent架构/LangChain |
| [[summaries/ai-agent-overview]] | AI Agent智能体概述：自主决策、工具调用、ReAct循环 | AI人工智能/Agent架构 |
| [[summaries/prompt-engineering]] | Prompt提示词工程：角色定义、任务拆解、模板系统 | AI人工智能/Prompt + RAG |

---

## 低可信度 Summaries (⚠ 未审核)

*来源：`raw/sources/Self learn/` - AI自主探索收集的网络博客，未经用户审核确认。*

| Summary | Description | Source |
|---------|-------------|--------|
| [[summaries/ai-infra-gpu-fabric]] | AI基础设施架构：GPU Fabric设计、控制平面vs数据平面、Training vs Inference分离 | Self learn/AI infra-Rack2Cloud |
| [[summaries/ai-infra-ai-factory]] | AI Factory五阶段演进：从Basic GPU Cluster到全自动化AI Factory | Self learn/AI infra-vCluster |
| [[summaries/ai-infra-compute-design]] | AI计算基础设施五层架构：GPU/RDMA/Storage/Orchestration/Monitoring | Self learn/AI infra-Scality |
| [[summaries/k8s-architecture-official]] | Kubernetes官方架构文档：控制平面、节点组件、插件 | Self learn/K8s云原生-官方 |
| [[summaries/multi-agent-architecture-patterns]] | Multi-Agent四种架构流派：Structuralists/Interactionists/Role-Players/Minimalists | Self learn/多agent编排-Comet |
| [[summaries/multi-agent-production-patterns]] | Multi-Agent四种核心模式与生产基础设施需求 | Self learn/多agent编排-TrueFoundry |
| [[summaries/harness-cicd-overview]] | Harness开源CI/CD系统：容器原生架构、AI增强自动化、企业实战案例 | Self learn/Harness-掘金 |
| [[summaries/harness-cdas-platform]] | Harness CDaaS平台体验：自动检测新版本、ML评估部署质量 | Self learn/Harness-腾讯云 |
| [[summaries/gitops-harness-architecture]] | GitOps架构详解：Harness + VKS + GitLab + Dynatrace + Vault + Wiz | Self learn/Harness-Broadcom |
| [[summaries/harness-pipeline-design]] | Harness CD Pipeline设计最佳实践（官方文档） | Self learn/Harness-官方 |
| [[summaries/agent-memory-langgraph]] | LangGraph Agent记忆系统：短期Checkpointer + 长期Store，Semantic/Episodic/Procedural类型 | Self learn/Agent记忆-LangGraph官方 |
| [[summaries/rag-production-practice]] | RAG生产实践：混合检索+重排+数据飞轮+评测体系，Advanced RAG三层优化 | Self learn/RAG生产-腾讯云 |
| [[summaries/plan-and-execute-agent]] | Plan-and-Execute Agent范式：Planner+Executor分离，ReWOO变量引用+LLMCompiler并行 | Self learn/PlanExecute-LangChain |
| [[summaries/agent-long-task-production]] | Agent长期任务生产失败：30秒同步边界+Checkpointer vs持久化执行+幂等性+35分钟衰减 | Self learn/Agent长期任务-TianPan |

> **使用建议**：低可信度内容需谨慎参考，建议先阅读原文验证。所有摘要 frontmatter 已添加 `credibility: low` 字段。

---

## Wiki 生成内容

*提问过程中 AI 基于笔记分析整理和自行搜索汇总生成的内容，存于 wiki 目录。*

| Summary | Description | 来源类型 |
|---------|-------------|----------|
| [[summaries/vdpa-interrupt-datapath]] | vDPA中断与数据面：硬件直通+virtio控制面 | Wiki知识整合（参考 virtio/kvm-live-migration 等） |

> **说明**：
> - Wiki 目录下的内容不进行可信度分级
> - `source_type: wiki-derived` 标识来源于 wiki 内已有知识的整理汇总
> - `source_files` 记录实际参考的 wiki 摘要文件

---

## Entities

*实体页面 - 人物、公司、产品、具体事物。*

(暂无实体页面。在来源处理过程中创建。)

---

## Concepts

*概念页面 - 主题、理论、框架、思想。*

| Concept | Description |
|---------|-------------|
| [[concepts/linux-spinlock]] | 自旋锁：忙等待锁，适用于中断和极短临界区 |
| [[concepts/linux-mutex]] | 互斥锁：睡眠锁，适用于进程上下文和长时间临界区 |
| [[concepts/linux-rcu]] | Read-Copy-Update：无锁机制，读者零开销，适用于读极多写极少 |
| [[concepts/linux-memory-management]] | Linux内存管理：分配接口、统计分类、黑洞问题 |
| [[concepts/cfs-scheduler]] | CFS：完全公平调度器，红黑树+vruntime（详见红黑树关联） |
| [[concepts/linux-scheduling-policy]] | Linux调度策略：SCHED_OTHER/FIFO/RR |
| [[concepts/linux-networking]] | Linux网络协议：TCP/IP分层模型、协议对比 |
| [[concepts/crash-analysis]] | crash工具分析技术，mutex owner解码、进程结构体查看 |
| [[concepts/registers-analysis]] | 寄存器分析技术，x86_64/ARM64调用约定、崩溃诊断 |
| [[concepts/kvm-virtualization]] | KVM虚拟化性能指标：%ST、EXT、超分场景分析 |
| [[concepts/task-struct]] | task_struct与mm_struct，进程/内存描述符结构关系 |
| [[concepts/virtio]] | Virtio核心概念：半虚拟化I/O框架，前后端通信机制 |
| [[concepts/ioeventfd-irqfd]] | ioeventfd与irqfd机制：KVM零拷贝通知机制 |
| [[concepts/vring]] | Vring环形缓冲区：desc/avail/used三表数据共享 |
| [[concepts/containerd]] | Containerd：工业级容器运行时，从Docker分离的管理层 |
| [[concepts/cri]] | CRI：Kubernetes容器运行时接口，解耦kubelet与运行时 |
| [[concepts/oci]] | OCI：开放容器标准，镜像结构+运行时接口规范 |
| [[concepts/binary-tree]] | 二叉树：最多两子节点的树结构，遍历是核心操作 |
| [[concepts/red-black-tree]] | 红黑树：弱平衡BST，Linux CFS/epoll核心数据结构 |
| [[concepts/btree]] | B树/B+树：多路搜索树，数据库/文件系统索引首选 |
| [[concepts/namespace]] | Namespace：Linux内核资源隔离，容器"看见什么" |
| [[concepts/cgroups]] | Cgroups：Linux内核资源限制，容器"能用多少" |
| [[concepts/kubernetes]] | Kubernetes：容器编排平台，Pod/Service/Deployment |
| [[concepts/langchain]] | LangChain：LLM应用开发框架，Runnable/LCEL/Agent/RAG |
| [[concepts/ai-agent]] | AI Agent智能体：自主决策、工具调用、ReAct循环 |
| [[concepts/rag]] | RAG检索增强生成：检索+生成，VectorStore/Retriever |
| [[concepts/lcel]] | LCEL表达式语言：`|`运算符链式组合Runnable |
| [[concepts/langgraph]] | LangGraph：图式Agent工作流，StateGraph/DAG/Checkpoint |
| [[concepts/prompt-engineering]] | Prompt工程：提示词设计、Few-shot、Chain-of-Thought |
| [[concepts/ai-skills]] | AI Skills技能库：Agent可调用的技能模块 |
| [[concepts/transformer]] | Transformer：现代LLM基础架构，Self-Attention |
| [[concepts/agent-memory]] | Agent Memory：短期/长期记忆，Semantic/Episodic/Procedural类型 |
| [[concepts/plan-and-execute]] | Plan-and-Execute：规划执行范式，Planner+Executor分离架构 |
| [[concepts/durable-execution]] | Durable Execution：持久化执行，自动故障恢复+幂等保证 |

---

## Meta

### 原始文档统计 (raw/sources/)

| 可信度 | 数量 | 说明 |
|--------|------|------|
| 高可信度 | 71 | 用户审核确认 |
| 低可信度 | 17 | Self learn，未审核 |
| **总计** | **88** | |

> **中可信度**：目前为 0。指提问过程中 AI 生成的**放在 raw 下**的文档，当前不存在此类文档。

### Wiki 内容统计

| 类型 | 数量 |
|------|------|
| Summaries (高可信度来源) | 41 |
| Summaries (低可信度来源) | 14 |
| Summaries (Wiki生成) | 1 |
| **Summaries 总计** | **56** |
| Concepts | 34 |

**Last Updated**: 2026-05-14 (自我反思: 已核实统计数据准确性，新增4个低可信度摘要+3个概念)

---

## Notes

### 源文件去重处理

以下源文件内容重复或已合并：
- `Linux 操作系统/Linux 网络协议栈.md` 与 `Linux 操作系统/Linux 网络/Linux 网络协议栈.md` → 合并到 `linux-network-stack`
- `Linux 操作系统/Linux IRQ中断.md` 与 `Linux 操作系统/Linux 中断系统/Linux IRQ中断.md` → 合并到 `linux-irq-interrupt`

### 未创建摘要的文件

**高可信度（待处理）：**
- `Linux 操作系统/linux学习结构.md` → 学习规划目录，非知识内容
- `数据结构与算法/图/图 合集.md` → 待创建摘要
- `数据结构与算法/算法合集.md` → 待创建摘要
- `DFX工具/==vmcore解析==/开源crash网站.md` → 待创建摘要
- `DFX工具/==vmcore解析==/调度sched.md` → 待创建摘要
- `DFX工具/==vmcore解析==/寄存器和地址分布.md` → 待创建摘要
- `AI人工智能/Agent架构/Claude Code 安装.md` → 待创建摘要
- `AI人工智能/Agent架构/LangChain/LangChain 解决的核心问题.md` → 待创建摘要
- `AI人工智能/Skills/my-skills.md` → 待创建摘要
- `AI人工智能/Skills/开源skills库.md` → 待创建摘要
- `Linux虚拟化/IO虚拟化/virtio相关博客.md` → 待创建摘要
- `Linux虚拟化/IO虚拟化/virtio-blk和virtio-scsi的理解.md` → 待创建摘要
- `Linux虚拟化/热迁移/热迁移命令.md` → 待创建摘要
- `Kunbernetes和Docker/K8s云原生-阿里云-K8S技术原理.md` → 待创建摘要

**低可信度（Self learn）：**
- ✅ 已全部创建摘要（10个），详见"低可信度 Summaries"章节

### 可信度分级说明

根据 [[CLAUDE.md]] 规范，可信度分级**仅针对原始文档** (`raw/` 目录)：

| 可信度 | 来源位置 | 说明 |
|--------|----------|------|
| **高** | `raw/sources/` 除 `Self learn` 外的目录 | 用户审核确认 |
| **中** | `raw/` 下的其他位置 | 提问过程中 AI 基于笔记分析整理和自行搜索汇总生成的文档 |
| **低** | `raw/sources/Self learn/` | AI 自主收集，未审核 |

> wiki 目录下的内容不进行可信度分级。
> 低可信度原始文档的摘要 frontmatter 应添加 `credibility: low` 字段。

---

## 健康检查报告 (2026-05-14)

### 🔗 断链问题

以下 wikilinks 指向不存在的页面，需要修复：

| 文件 | 断链 | 问题说明 |
|------|------|----------|
| [[summaries/dfx-tools-overview]] | `[[summaries/bpftrace-tool]]` | 引用了不存在的 bpftrace-tool 概念页面 |
| [[concepts/lcel]] | `[[concepts/runnable]]` | 引用了不存在的 Runnable 抽象概念页面 |
| [[recommendations/2026-05-14]] | `[[Day learn]]` | 已修复为 `[[recommendations/1-Day learn]]` |

**建议修复方案**：
- 创建缺失的概念页面（bpftrace-tool、runnable）
- 或移除/修正断链引用

### 🏝️ 孤立页面

以下页面仅在 index.md 中列出，缺少其他页面的交叉引用：

| 页面 | 建议增加链接位置 |
|------|------------------|
| [[concepts/task-struct]] | vmcore-analysis、memory-analysis-tools |
| [[concepts/registers-analysis]] | vmcore-analysis、gdb-debugging |
| [[summaries/virtio-net-forwarding]] | virtio-architecture、virtio-device-types |
| [[summaries/virtio-device-init]] | virtio-architecture、virtio-vring-mechanism |
| [[summaries/vgic-interrupt-virtualization]] | kvm-interrupt-injection |
| [[summaries/tcpdump-tool]] | network-tools、dfx-tools-overview |

**建议操作**：为孤立页面添加相关页面的交叉引用，增强知识图谱连通性。

### ✅ 已通过检查

- ✓ 42 个 Summaries 页面全部存在于 wiki 目录
- ✓ 31 个 Concepts 页面全部存在于 wiki 目录
- ✓ 索引统计数据与实际文件数量一致
- ✓ 高可信度源文件数量：71（不含 Self learn）
- ✓ 低可信度源文件数量：10（Self learn 目录）