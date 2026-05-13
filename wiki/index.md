# Wiki Index

Content catalog. Auto-updated on every ingest.

---

## Summaries

*Source document summaries - entry point for each raw source.*

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
| [[summaries/tcpdump-tool]] | tcpdump网络抓包分析：过滤表达式、参数详解 | DFX工具/==网络== |
| [[summaries/vdpa-interrupt-datapath]] | vDPA中断与数据面：硬件直通+virtio控制面 | 问答整理 |
| [[summaries/binary-tree-basics]] | 二叉树基础：形态、遍历、存储、高频题型 | 数据结构与算法/树 |
| [[summaries/red-black-tree-detailed]] | 红黑树详解：性质、插入删除操作、与B树等价 | 数据结构与算法/树 |
| [[summaries/avl-btree-overview]] | AVL树与B树/B+树：对比、数据库索引选择 | 数据结构与算法/树 |

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

---

## Meta

- **Total Sources**: 66
- **Total Summaries**: 39
- **Total Concepts**: 23
- **Last Updated**: 2026-05-13

---

## Notes

### 源文件去重处理

以下源文件内容重复或已合并：
- `Linux 操作系统/Linux 网络协议栈.md` 与 `Linux 操作系统/Linux 网络/Linux 网络协议栈.md` → 合并到 `linux-network-stack`
- `Linux 操作系统/Linux IRQ中断.md` 与 `Linux 操作系统/Linux 中断系统/Linux IRQ中断.md` → 合并到 `linux-irq-interrupt`

### 未创建摘要的文件

- `Linux 操作系统/linux学习结构.md` → 学习规划目录，非知识内容