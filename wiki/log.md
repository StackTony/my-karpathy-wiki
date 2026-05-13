# Wiki Log

Chronological record of wiki operations. Append-only.

Format: `## [YYYY-MM-DD] operation | Title`

---

## [2026-05-02] init | Wiki Created

- Initialized LLM Wiki structure
- Created CLAUDE.md schema
- Created index.md and log.md
- Wiki ready for first source ingestion

## [2026-05-11] ingest | Linux操作系统系列文档

- Created summaries/linux-irq-interrupt.md
- Created summaries/linux-network-stack.md
- Created summaries/linux-io-mechanism.md
- Created summaries/linux-lock-mechanisms.md
- Created concepts/linux-spinlock.md
- Created concepts/linux-mutex.md
- Created concepts/linux-rcu.md
- Updated index.md with new entries
- Source: `raw/sources/Linux 操作系统/` (含中断、网络、IO、锁机制等子目录)

## [2026-05-11] ingest | DFX工具系列文档

- Created summaries/dfx-tools-overview.md (工具总览)
- Created summaries/perf-tool.md (perf性能分析)
- Created summaries/flamegraph-tool.md (火焰图可视化)
- Created summaries/ftrace-kprobe-tools.md (内核追踪)
- Created summaries/kvmtop-tool.md (虚拟机监控)
- Created summaries/vmcore-analysis.md (崩溃转储解析)
- Created summaries/gdb-debugging.md (GDB调试)
- Created summaries/io-tools.md (IO性能工具)
- Created summaries/memory-analysis-tools.md (内存分析)
- Created summaries/interrupt-monitoring.md (中断监控)
- Created summaries/network-tools.md (网络工具)
- Created concepts/crash-analysis.md (crash分析技术)
- Created concepts/registers-analysis.md (寄存器分析)
- Created concepts/kvm-virtualization.md (KVM性能指标)
- Created concepts/task-struct.md (进程结构体)
- Updated index.md with all new entries
- Source: `raw/sources/DFX工具/` (含CPU、Trace、内存、vmcore、IO、网络、gdb等子目录)

## [2026-05-11] ingest | Linux锁机制详细文档

- Sources already covered in existing concepts (Mutex/SpinLock/RCU detailed docs)
- Enhanced existing concept pages with additional crash analysis details
- Source: `raw/sources/Linux 操作系统/Linux 锁机制/`

## [2026-05-11] ingest | Linux IO调度算法

- Created summaries/linux-io-scheduler.md
- Source: `raw/sources/Linux 操作系统/Linux IO机制/Linux IO调度算法.md`

## [2026-05-11] ingest | Linux虚拟化系列文档

- Created summaries/virtio-architecture.md (Virtio整体架构)
- Created summaries/virtio-notification-mechanism.md (ioeventfd+irqfd通知机制)
- Created summaries/virtio-vring-mechanism.md (Vring数据共享机制)
- Created summaries/virtio-device-types.md (virtio-blk/scsi/net设备类型)
- Created summaries/kvm-interrupt-injection.md (KVM中断注入机制)
- Created summaries/kvm-live-migration.md (热迁移机制)
- Created concepts/virtio.md (Virtio核心概念)
- Created concepts/ioeventfd-irqfd.md (零拷贝通知机制)
- Created concepts/vring.md (Vring环形缓冲区)
- Updated index.md with all new entries
- Source: `raw/sources/Linux 虚拟化/` (含IO虚拟化、中断虚拟化、网络虚拟化、热迁移等子目录)

## [2026-05-12] ingest | Containerd容器运行时

- Created summaries/containerd-runtime.md (Containerd运行时完整指南)
- Created concepts/containerd.md (Containerd核心概念)
- Created concepts/cri.md (CRI容器运行时接口)
- Created concepts/oci.md (OCI开放容器标准)
- Updated summaries/kvm-live-migration.md (新增网络迁移时序图、详细流程)
- Updated index.md with new entries
- Source: `raw/sources/Kunbernetes和Docker/容器运行时 containerd.md` (新增)
- Source: `raw/sources/Linux 虚拟化/热迁移/热迁移流程.md` (修改)

## [2026-05-12] ingest | Linux内存管理与进程调度

- Created summaries/linux-meminfo.md (/proc/meminfo详解)
- Created summaries/linux-scheduler.md (进程调度器、CFS机制)
- Created concepts/linux-memory-management.md (内存分配接口、黑洞问题)
- Created concepts/cfs-scheduler.md (完全公平调度器、红黑树、vruntime)
- Created concepts/linux-scheduling-policy.md (SCHED_OTHER/FIFO/RR三种策略)
- Updated index.md with new entries
- Source: `raw/sources/Linux 操作系统/Linux 内存管理/Linux meminfo参数详细解释.md` (新增)
- Source: `raw/sources/Linux 操作系统/Linux 进程调度/Linux 进程调度器.md` (新增)
- Source: `raw/sources/Linux 操作系统/Linux 进程调度/Linux 进程调度策略.md` (新增)
- Note: `linux学习结构.md` 为学习规划目录，非知识内容，未创建摘要

## [2026-05-12] query | Linux常用网络协议问答

- 用户提问："Linux常用的网络协议有哪些"
- 综合已有知识整理回答，包含TCP/UDP/IP/ICMP/ARP等协议详解
- 将有价值回答存回wiki
- Created summaries/linux-network-protocols.md
- Created concepts/linux-networking.md
- Updated index.md with new entries

## [2026-05-12] ingest | Linux网络与中断补充

- Updated summaries/linux-network-protocols.md（新增LACP链路聚合协议）
- Updated summaries/linux-irq-interrupt.md（确认IPI内容已覆盖）
- 新增源文件：
  - `raw/sources/Linux 操作系统/Linux 网络/Linux 常见的网络协议.md`（已合并到现有摘要）
  - `raw/sources/Linux 操作系统/Linux 网络/Linux 网络协议栈.md`（已有摘要覆盖）
  - `raw/sources/Linux 操作系统/Linux 中断系统/Linux IRQ中断.md`（已有摘要覆盖）

## [2026-05-12] full-sync | 全量更新索引

全量对比检查raw/sources与wiki内容：

**新增摘要（4篇）**：
- summaries/vgic-interrupt-virtualization.md（VGIC中断虚拟化）
- summaries/device-passthrough.md（IOMMU/SR-IOV/VFIO/vDPA设备直通）
- summaries/virtio-device-init.md（Virtio设备初始化流程）
- summaries/virtio-net-forwarding.md（virtio-net内核态网络转发）

**去重处理**：
- `Linux IRQ中断.md`：两处重复源文件（Linux操作系统/ 和 Linux中断系统/），合并为同一摘要
- `Linux 网络协议栈.md`：两处重复源文件，合并为同一摘要
- `linux学习结构.md`：学习规划目录，跳过

**索引更新**：
- 更新wiki/index.md，整合所有摘要
- 添加去重说明章节
- 统计更新：Sources 56, Summaries 30, Concepts 17

## [2026-05-13] ingest | 数据结构与算法系列文档

**新增摘要（3篇）**：
- summaries/binary-tree-basics.md（二叉树基础：形态、遍历、存储、高频题型）
- summaries/red-black-tree-detailed.md（红黑树详解：五大性质、插入删除操作、与B树等价性）
- summaries/avl-btree-overview.md（AVL树与B树/B+树：对比、数据库索引选择原理）

**新增概念（3篇）**：
- concepts/binary-tree.md（二叉树核心概念）
- concepts/red-black-tree.md（红黑树：Linux CFS/epoll核心数据结构）
- concepts/btree.md（B树/B+树：多路搜索树，数据库索引首选）

**更新概念**：
- concepts/cfs-scheduler.md：新增红黑树关联章节，说明CFS选择红黑树的原因

**知识图谱关联**：
- 二叉树 → 红黑树 → CFS调度器（数据结构支撑系统模块）
- B+树 → 数据库索引 → IO调度（磁盘存储结构）

**索引更新**：
- 更新wiki/index.md，新增数据结构与算法分类
- 统计更新：Sources 61, Summaries 33, Concepts 20
- Source: `raw/sources/数据结构与算法/`（含树、图、算法合集子目录）

## [2026-05-13] ingest | Linux资源隔离系列文档

**新增摘要（1篇）**：
- summaries/linux-namespace-cgroups.md（Namespace资源隔离+Cgroups资源限制，容器技术基石）

**新增概念（2篇）**：
- concepts/namespace.md（Namespace：Linux内核资源隔离，容器"看见什么"）
- concepts/cgroups.md（Cgroups：Linux内核资源限制，容器"能用多少"）

**更新摘要/概念**：
- summaries/containerd-runtime.md：新增Namespace/Cgroups关联链接
- concepts/cfs-scheduler.md：新增Cgroups cpu子系统关联章节

**知识图谱关联**：
- Namespace+Cgroups → 容器隔离机制 → containerd运行时
- Cgroups cpu子系统 → CFS调度器 → cpu.cfs_quota_us限制

**索引更新**：
- 更新wiki/index.md，新增Linux资源隔离分类
- 统计更新：Sources 62, Summaries 34, Concepts 22
- Source: `raw/sources/Linux 操作系统/Linux 资源隔离/Linux Namespace与Cgroups介绍.md`

## [2026-05-13] refactor | sources字段格式统一

**规范更新**：
- CLAUDE.md新增sources格式规范章节
- 采用 `source_dir` + `source_files` 分离格式

**格式示例**：
```yaml
source_dir: 数据结构与算法/树
source_files: [红黑树详解.md]
```

**批量更新**：
- 33个摘要全部采用新格式
- 精确追溯每个来源文件
- 统计更新：所有摘要frontmatter格式一致

## [2026-05-13] query | vDPA中断与数据面机制问答

**用户提问**："vdpa的中断控制面和数据面是怎么运作的？"

**回答整理**：
- 创建 summaries/vdpa-interrupt-datapath.md（vDPA中断与数据面：硬件直通+virtio控制面）
- 内容涵盖：数据面DMA直通、控制面virtio标准接口、直接中断注入、热迁移兼容性

**知识图谱关联**：
- vDPA数据面 → [[summaries/virtio-vring-mechanism]]（硬件直接操作Vring）
- vDPA中断 → [[summaries/kvm-interrupt-injection]]（直接中断注入机制）
- vDPA热迁移 → [[summaries/kvm-live-migration]]（virtio标准状态迁移）

**索引更新**：
- 更新wiki/index.md
- 统计更新：Sources 62, Summaries 35, Concepts 22

## [2026-05-13] ingest | 新增资料全量更新

**新增摘要（4篇）**：
- summaries/tcpdump-tool.md（tcpdump网络抓包分析：过滤表达式、参数详解）
- summaries/kubernetes-core-concepts.md（Kubernetes核心概念与架构：Pod/Service/Deployment）
- summaries/linux-boot-process.md（Linux启动详细过程：BIOS→MBR→GRUB→内核→init十步流程）

**新增概念（1篇）**：
- concepts/kubernetes.md（Kubernetes：容器编排平台核心概念）

**更新摘要**：
- summaries/device-passthrough.md：源文件名变更（设备直通 iommu+sriov、vfio+vdpa.md → 设备直通 iommu+sriov、vfio.md）

**知识图谱关联**：
- Kubernetes → [[summaries/containerd-runtime]]（容器运行时）
- Kubernetes → [[concepts/cri]]（CRI接口）
- Kubernetes → [[concepts/namespace]] + [[concepts/cgroups]]（底层隔离机制）
- Linux启动 → [[summaries/linux-scheduler]]（init进程PID=1）
- tcpdump → [[summaries/network-tools]]（网络工具）

**索引更新**：
- 更新wiki/index.md，新增Kubernetes分类、Linux系统启动分类
- 统计更新：Sources 65, Summaries 38, Concepts 23
- Source: `raw/sources/Kunbernetes和Docker/Kubernetes（K8s）全面解析：核心概念、架构与实践.md`
- Source: `raw/sources/Linux操作系统/Linux系统启动/Linux启动详细过程（开机启动顺序）.md`
- Source: `raw/sources/DFX工具/==网络==/网络分析工具tcpdump.md`

## [2026-05-13] ingest | Linux关机流程与目录调整

**新增摘要（1篇）**：
- summaries/linux-shutdown-process.md（Linux关机流程深度解析：进程终止→文件系统卸载→ACPI断电）

**更新摘要（目录变化）**：
- summaries/linux-boot-process.md：源目录变化（Linux系统启动 → Linux系统启动关闭）

**知识图谱关联**：
- Linux关机 → [[summaries/linux-boot-process]]（启动与关机对称流程）
- Linux关机 → [[concepts/cgroups]]（systemd使用cgroups批量终止）
- Linux关机 → [[concepts/namespace]]（容器关机协调命名空间）

**索引更新**：
- 更新wiki/index.md
- 统计更新：Sources 66, Summaries 39, Concepts 23
- Source: `raw/sources/Linux操作系统/Linux系统启动关闭/Linux关机流程深度解析：从内核机制到硬件控制的完整理论框架.md`