---
title: DFX 工具总览
created: 2026-05-11
updated: 2026-05-13
tags: [DFX, 调试, 性能分析, Linux]
source_dir: DFX工具
source_files: [==CPU==/火焰图抓取CPU占用情况.md, ==CPU==/kprobe抓CPU单核调度轨迹.md]
---

# DFX 工具总览

DFX（Design for X）工具集是 Linux 系统性能分析、调试和问题诊断的核心工具链。

## 工具分类

### CPU 分析工具
- [[summaries/perf-tool]] - 性能事件采样、火焰图生成
- [[summaries/flamegraph-tool]] - CPU 占用可视化分析
- [[summaries/kvmtop-tool]] - 虚拟机 CPU/EXT 指标监控

### Trace 追踪工具
- [[summaries/ftrace-kprobe-tools]] - 内核函数追踪、动态探测
- **bpftrace** - 高级动态追踪（BPF），详见 ftrace-kprobe-tools

### 内存分析工具
- perf slab 分析 - slab 内存占用
- numastat - NUMA 内存分布
- 内存预占分析 - reserved/absent 内存

### vmcore 解析工具
- [[summaries/vmcore-analysis]] - crash 工具、寄存器分析、进程结构

### IO 分析工具
- iostat - IO 使用率、时延监控
- fio - IO 性能测试工具
- blktrace - IO 流程追踪

### 网络分析工具
- iperf - 网络带宽测试

### 调试工具
- [[summaries/gdb-debugging]] - QEMU/内核调试、常用命令

## 核心概念

### 虚拟化性能指标
- **%ST (Steal Time)**: 虚拟机等待宿主机 CPU 资源的时间占比
- **EXT (VM-Exit)**: 虚拟机退出原因统计（HVC、WFE/WFI、MMIO、IRQ 等）

### 性能分析流程
```
现象发现 → 工具定位 → 数据采集 → 结果分析 → 根因确认
    ↓           ↓           ↓           ↓
 top/iostat   perf/ftrace  火焰图/vmcore  代码分析
```

## 快速选择指南

| 问题类型 | 推荐工具 | 典型命令 |
|---------|---------|---------|
| CPU 占用高 | perf + 火焰图 | `perf record -g -a -- sleep 30` |
| CPU 调度问题 | perf sched | `perf sched record -g -p PID` |
| 函数调用追踪 | ftrace/kprobe | `echo 'p:my_probe func' > kprobe_events` |
| 内存泄漏 | perf kmem | `perf kmem --alloc --caller record` |
| vmcore 分析 | crash | `crash vmcore vmlinux` |
| IO 瓶颈 | iostat/fio | `iostat -dmx 1 5 /dev/sda` |
| 网络带宽 | iperf | `iperf -c IP -u -b 50G` |

## 相关链接

- [[concepts/linux-spinlock]]
- [[concepts/linux-mutex]]
- [[concepts/linux-rcu]]
- [[summaries/linux-irq-interrupt]]