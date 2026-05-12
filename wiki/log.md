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