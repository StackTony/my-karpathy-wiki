# Wiki Log

Chronological record of wiki operations. Append-only.

Format: `## [YYYY-MM-DD] operation | Title`

---

## [2026-05-02] init | Wiki 创建

- 初始化 LLM Wiki 结构
- 创建 CLAUDE.md、index.md、log.md

---

## [2026-05-11] ingest | Linux 操作系统 + DFX工具 + Linux虚拟化

**新增摘要（18篇）**：
- Linux操作系统：irq-interrupt、network-stack、io-mechanism、lock-mechanisms、io-scheduler
- DFX工具：dfx-tools-overview、perf-tool、flamegraph-tool、ftrace-kprobe-tools、kvmtop-tool、vmcore-analysis、gdb-debugging、io-tools、memory-analysis-tools、interrupt-monitoring、network-tools
- Linux虚拟化：virtio-architecture、virtio-notification-mechanism、virtio-vring-mechanism、virtio-device-types、kvm-interrupt-injection、kvm-live-migration

**新增概念（11篇）**：linux-spinlock、linux-mutex、linux-rcu、crash-analysis、registers-analysis、kvm-virtualization、task-struct、virtio、ioeventfd-irqfd、vring

**来源**：
- `raw/sources/Linux 操作系统/`（中断、网络、IO、锁机制）
- `raw/sources/DFX工具/`（CPU、Trace、内存、vmcore、IO、网络）
- `raw/sources/Linux 虚拟化/`（IO虚拟化、中断虚拟化、热迁移）

---

## [2026-05-12] ingest | Containerd + Linux内存管理/进程调度 + query网络协议

**新增摘要（4篇）**：containerd-runtime、linux-meminfo、linux-scheduler、linux-network-protocols

**新增概念（6篇）**：containerd、cri、oci、linux-memory-management、cfs-scheduler、linux-scheduling-policy、linux-networking

**更新摘要**：kvm-live-migration（新增网络迁移时序图）

**来源**：
- `raw/sources/Kunbernetes和Docker/容器运行时 containerd.md`
- `raw/sources/Linux 操作系统/Linux 内存管理/Linux meminfo参数详细解释.md`
- `raw/sources/Linux 操作系统/Linux 进程调度/`

**Query回答存档**：Linux常用网络协议问答 → summaries/linux-network-protocols.md

---

## [2026-05-12] full-sync | 全量更新索引

**新增摘要（4篇）**：vgic-interrupt-virtualization、device-passthrough、virtio-device-init、virtio-net-forwarding

**去重处理**：
- Linux IRQ中断.md：两处重复源文件合并
- Linux 网络协议栈.md：两处重复源文件合并

**统计更新**：Sources 56, Summaries 30, Concepts 17

---

## [2026-05-13] ingest | 数据结构算法 + Linux资源隔离 + sources格式统一 + query vDPA

**新增摘要（7篇）**：
- 数据结构：binary-tree-basics、red-black-tree-detailed、avl-btree-overview
- Linux资源隔离：linux-namespace-cgroups
- query存档：vdpa-interrupt-datapath

**新增概念（5篇）**：binary-tree、red-black-tree、btree、namespace、cgroups

**更新概念**：cfs-scheduler（新增红黑树关联）

**refactor**：sources字段格式统一（`source_dir` + `source_files` 分离），33个摘要批量更新

**来源**：
- `raw/sources/数据结构与算法/`
- `raw/sources/Linux 操作系统/Linux 资源隔离/`

**统计更新**：Sources 62, Summaries 35, Concepts 22

---

## [2026-05-13] ingest | K8s + tcpdump + Linux启动 + Linux关机

**新增摘要（4篇）**：tcpdump-tool、kubernetes-core-concepts、linux-boot-process、linux-shutdown-process

**新增概念（1篇）**：kubernetes

**来源**：
- `raw/sources/Kunbernetes和Docker/Kubernetes（K8s）全面解析.md`
- `raw/sources/Linux操作系统/Linux系统启动关闭/`

**统计更新**：Sources 66, Summaries 39, Concepts 23

---

## [2026-05-13] ingest | AI人工智能系列

**新增领域**：AI人工智能

**新增摘要（3篇）**：langchain-architecture、ai-agent-overview、prompt-engineering

**新增概念（8篇）**：langchain、ai-agent、rag、lcel、langgraph、prompt-engineering、ai-skills、transformer

**来源**：`raw/sources/AI人工智能/`（Agent架构、Prompt+RAG、Skills、大模型）

**统计更新**：Sources 74, Summaries 42, Concepts 31

---

## [2026-05-14] config + recommend + ingest | 学习推荐系统 + Self learn摘要

**系统配置**：
- 定时任务：07:00 自学推荐、09:00 推送报告、23:00 每3天 Ingest检查
- 新增目录：`wiki/recommendations/`、`raw/sources/Self learn/`
- CLAUDE.md 新增 Learn 工作流程

**自学推荐执行**：
- 推荐主题：AI infra、多agent编排、K8s云原生、Harness、Agent开发技术栈
- 报告：wiki/recommendations/2026-05-14.md、2026-05-14-Agent-Deep-Learn.md

**Self learn摘要创建（14篇）**：
- AI infra：ai-infra-gpu-fabric、ai-infra-ai-factory、ai-infra-compute-design
- Multi-Agent：multi-agent-architecture-patterns、multi-agent-production-patterns
- K8s：k8s-architecture-official
- Harness：harness-cicd-overview、harness-cdas-platform、gitops-harness-architecture、harness-pipeline-design
- Agent深度：agent-memory-langgraph、rag-production-practice、plan-and-execute-agent、agent-long-task-production

**新增概念（3篇）**：agent-memory、plan-and-execute、durable-execution

**可信度标记**：所有 Self learn 摘要添加 `credibility: low`

**统计更新**：Sources 88, Summaries 56（42高+14低），Concepts 34

---

## [2026-05-14] lint + fix | 健康检查与修复

**断链修复（3个）**：
- `[[summaries/bpftrace-tool]]` → 待创建
- `[[concepts/runnable]]` → 待创建
- `[[recommendations/1-Day learn]]` 链接统一（文件更名为 1-Day learn.md）

**交叉引用补充**：AI infra系列、Multi-Agent系列、Harness系列内部关联链接

**source_files修复**：vdpa-interrupt-datapath.md 补充来源 + 新增 `source_type: wiki-derived`

**孤立页面（6个）**：task-struct、registers-analysis、virtio-net-forwarding、virtio-device-init、vgic-interrupt-virtualization、tcpdump-tool

---

## [2026-05-15] recommend | 自学推荐（无指定主题）

**触发条件**：`wiki/recommendations/1-Day learn.md` 文件不存在或为空

**知识缺口分析**：
- 数据结构：图论（Graph）知识缺失 → 高优先级
- 虚拟化：virtio-blk vs virtio-scsi 区分 → 高优先级
- Kubernetes：K8s技术原理深化 → 中优先级
- DFX工具：vmcore调度分析实战 → 中优先级
- AI Agent：Skills系统实战 → 中优先级
- 算法：算法设计模式 → 低优先级

**联网搜索**：
- 来源：Tavily Search MCP
- 搜索主题：图论基础、virtio-blk/scsi、K8s架构、Agent Skills、vmcore调度、算法模式
- 结果数量：25篇技术博客

**报告产出**：`wiki/recommendations/2026-05-15.md`

**推荐博客来源分布**：
- CSDN：8篇
- 知乎专栏：3篇
- 腾讯云：2篇
- GitHub：1篇
- 个人博客：2篇
- 官方文档：2篇（RedHat、K8s中文社区）
- 掘金：2篇
- 其他：5篇

**建议产出**（6个待处理源文件）：
- `数据结构与算法/图/图 合集.md` → concepts/graph + summaries/graph-basics
- `Linux 虚拟化/IO虚拟化/virtio-blk和virtio-scsi的理解.md` → summaries/virtio-blk-vs-scsi
- `Kunbernetes和Docker/K8s云原生-阿里云-K8S技术原理.md` → summaries/k8s-technical-principles
- `AI人工智能/Skills/my-skills.md` → summaries/my-skills-collection
- `数据结构与算法/算法合集.md` → concepts/algorithm-patterns + summaries/algorithm-design-patterns
- `DFX工具/==vmcore解析==/调度sched.md` → summaries/sched-vmcore-analysis

---

## [2026-05-16] recommend | 硬中断/软中断/ksoftirq 学习推荐

**触发条件**：[[recommendations/1-Day learn]] 指定主题：硬中断、软中断、ksoftirq机制

**已有知识分析**：
- [[summaries/linux-irq-interrupt]] 覆盖IRQ基础、IPI核间中断
- 缺失：软中断机制、ksoftirqd线程、上下半部分割机制

**联网搜索**：Tavily Search，10篇技术博客

**推荐博客（5篇）**：
- ArthurChiao：IRQ/softirq原理及内核实现（技术分析）
- 博客园：ksoftirqd详解（原理介绍）
- Ty-Chen：中断学习笔记（技术分析）
- HJK-Life：中断处理详解（技术分析）
- 腾讯云：软中断详解（原理介绍）

**已下载博客（5篇）**：
- `raw/sources/Self learn/中断-ArthurChiao-IRQ softirq原理.md`
- `raw/sources/Self learn/中断-博客园-ksoftirqd详解.md`
- `raw/sources/Self learn/中断-TyChen-中断学习笔记.md`
- `raw/sources/Self learn/中断-HJK-Life-中断处理详解.md`
- `raw/sources/Self learn/中断-腾讯云-软中断详解.md`

**报告产出**：`wiki/recommendations/2026-05-16.md`

---

*日志已合并压缩，保留关键信息与时间追溯能力*