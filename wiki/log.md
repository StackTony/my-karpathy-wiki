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