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