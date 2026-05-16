# Wiki Log

Chronological record of wiki operations. Key operations only.

---

## [2026-05-02] init | Wiki创建

- 初始化结构：CLAUDE.md、index.md、log.md

---

## [2026-05-11 ~ 05-13] ingest | 基础知识库构建

**新增摘要（36篇）**：
- Linux：irq-interrupt、network-stack、io-mechanism、lock-mechanisms、io-scheduler、meminfo、scheduler、namespace-cgroups、boot-process、shutdown-process
- DFX：dfx-tools、perf、flamegraph、ftrace-kprobe、kvmtop、vmcore、gdb、io-tools、memory-tools、interrupt-monitor、network-tools、tcpdump
- 虚拟化：virtio-architecture、notification、vring、device-types、device-init、net-forwarding、kvm-interrupt、vgic、live-migration、device-passthrough
- K8s/Docker：containerd-runtime、kubernetes-core
- 数据结构：binary-tree、red-black-tree、avl-btree
- AI：langchain-architecture、ai-agent-overview、prompt-engineering

**新增概念（24篇）**：spinlock、mutex、rcu、crash、registers、kvm、task-struct、virtio、ioeventfd、vring、containerd、cri、oci、mem-mgmt、sched-policy、networking、binary-tree、rbtree、btree、namespace、cgroups、cfs、kubernetes、langchain、ai-agent、rag、lcel、langgraph、prompt、skills、transformer

---

## [2026-05-14] config + learn | 学习推荐系统上线

**系统配置**：定时任务（07:00自学推荐、08:00自建更新）、recommendations目录、Self learn目录

**Self learn摘要（11篇）**：ai-infra系列、multi-agent系列、harness系列、agent-memory、plan-execute、agent-long-task

**新增概念（3篇）**：agent-memory、plan-and-execute、durable-execution

---

## [2026-05-16] learn + ingest | RAG技术深入

**自学推荐**：Chunk算法、召回重排、数据飞轮 → 13篇博客下载

**新增摘要（7篇）**：
- linux-softirq-mechanism、rag-full-stack-introduction、registers-crash-analysis、virtio-blk-vs-scsi、k8s-technical-principles
- chunk-strategies-deep-dive、rerank-production-practice、data-flywheel-design

**新增概念（10篇）**：softirq、ksoftirqd、rrf、gitops、ebpf、service-mesh、vibe-coding、chunk-strategies、rerank、data-flywheel

---

## [2026-05-16] lint | 健康检查与修复

**断链修复**：`rag-production-practice` → `rerank-production-practice`（3处）

**重复删除**：rag-production-practice.md（source_files错误）

**统计核实**：
- 原始文档：86高 + 26低 = 112
- Summaries：46高 + 15低 + 2Wiki = 63 ✓
- Concepts：46 ✓
- 孤立页面：无 ✓

---

## 当前统计 (2026-05-16)

| 类型 | 数量 |
|------|------|
| 原始文档 | 112（86高 + 26低） |
| Summaries | 63（46高 + 15低 + 2Wiki） |
| Concepts | 46 |

---

*日志已压缩，按日期合并，保留关键信息*