# Wiki Index

Content catalog. Auto-updated on every ingest.

---

## Summaries

*Source document summaries - entry point for each raw source.*

| Summary | Description | Source |
|---------|-------------|--------|
| [[summaries/linux-irq-interrupt]] | Linux IRQ中断机制，核间中断IPI | Linux IRQ中断.md |
| [[summaries/linux-network-stack]] | Linux网络协议栈完整流程，sk_buff核心结构 | Linux 网络协议栈.md |
| [[summaries/linux-io-mechanism]] | Linux IO栈架构，Block层→SCSI层→驱动→硬件 | Linux IO全景介绍.md |
| [[summaries/linux-lock-mechanisms]] | Linux内核锁机制全景对比，Spinlock/Mutex/RCU等 | Linux 锁机制全景介绍.md |
| [[summaries/linux-io-scheduler]] | Linux IO调度算法：noop/deadline/cfq/mq-deadline/bfq | Linux IO调度算法.md |
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
| [[summaries/virtio-architecture]] | Virtio整体架构：前后端、Vring、性能演进 | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-notification-mechanism]] | Virtio消息通知：ioeventfd+irqfd零拷贝机制 | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-vring-mechanism]] | Virtio Vring数据共享：desc/avail/used环形缓冲 | Linux虚拟化/IO虚拟化 |
| [[summaries/virtio-device-types]] | Virtio设备类型：virtio-blk/scsi/net、vDPA直通 | Linux虚拟化/IO虚拟化 |
| [[summaries/kvm-interrupt-injection]] | KVM中断注入机制：VGIC、irqfd、路由表 | Linux虚拟化/中断虚拟化 |
| [[summaries/kvm-live-migration]] | KVM热迁移机制：内存拷贝三阶段、网络恢复时序 | Linux虚拟化/热迁移 |
| [[summaries/containerd-runtime]] | Containerd容器运行时：CRI/OCI、架构与ctr命令 | Kunbernetes和Docker |

---

## Entities

*Entity pages - people, companies, products, specific things.*

(No entities yet. Created during source ingestion.)

---

## Concepts

*Concept pages - topics, theories, frameworks, ideas.*

| Concept | Description |
|---------|-------------|
| [[concepts/linux-spinlock]] | 自旋锁：忙等待锁，适用于中断和极短临界区 |
| [[concepts/linux-mutex]] | 互斥锁：睡眠锁，适用于进程上下文和长时间临界区 |
| [[concepts/linux-rcu]] | Read-Copy-Update：无锁机制，读者零开销，适用于读极多写极少 |
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

---

## Meta

- **Total Sources**: 49
- **Total Summaries**: 23
- **Total Concepts**: 13
- **Last Updated**: 2026-05-12