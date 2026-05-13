---
title: kvmtop 虚拟机监控工具
created: 2026-05-11
updated: 2026-05-13
tags: [kvmtop, KVM, 虚拟化, EXT, DFX]
source_dir: DFX工具/==CPU==
source_files: [kvmtop的 EXT 各项的理解.md, 分析虚拟机的%ST抢占.md, 查看当前某些CPU上跑的哪些进程.md, 获取qemu所有处于D状态的vcpu和线程.md]
---

# kvmtop 虚拟机监控工具

kvmtop 是 KVM 虚拟机性能监控工具，提供 CPU、EXT 等虚拟化特有指标。

## EXT 指标详解

EXT（VM-Exit）统计虚拟机退出原因：

| EXT 类型 | 说明 | 分析方法 |
|----------|------|---------|
| EXThvc | 虚拟机主动调用 Hypervisor（HVC 指令） | 正常系统调用 |
| EXTwfe/EXTwfi | 等待事件/中断（WFE/WFI 指令） | 关注 halt-polling 特性 |
| EXTmmioU/EXTmmioK | 内存映射 I/O 访问退出，IOMMU 相关 | MMIO 设备访问 |
| EXTfp | 浮点/向量指令导致的退出 | 浮点运算密集 |
| **EXTirq** | 虚拟机内的核间中断高 | 使用 irqtop 或 `/proc/interrupts` 分析 |
| **EXTsys64** | 64 位系统调用（SVC 指令） | 应用发起 read/write 等 |
| EXTmabt | 内存访问异常，通常是缺页访问 | 内存缺页 |

## EXTirq 分析

当 EXTirq 值异常时：

```bash
# 1. 虚拟机内查看中断统计
irqtop   # 或 cat /proc/interrupts

# 2. 对比正常与问题时期的中断量差异
# 关注:
# - IPI_RESCHEDULE（调度中断）
# - IPI_CALL_FUNC（函数调用中断）
# - virtio 相关设备中断
```

## %ST (Steal Time) 指标

| 来源 | 含义 | 采集方式 |
|------|------|---------|
| top %ST | 物理机整体 CPU 抢占 | `/proc/stat` steal 字段差值 |
| kvmtop %ST | 单个虚拟机 CPU 抢占 | x86: `/var/run/sysinfo/kvmtop/kvmtop_info`<br>arm: `kvmtop -b -n 2 -z` |

### 告警阈值
- **CPU 被抢占时间占比**：连续三个周期 ≥ 20% 告警
- **采集周期**：1 分钟
- 推荐自定义阈值：> 10% 告警

## 虚拟机 CPU 状态检查

### D 状态线程查找
```bash
ps -eL -o pid,tid,psr,state,comm,cmd | grep -E '(KVM|qemu)' | grep "D "
```

### 特定 CPU 上运行的进程
```bash
ps -eL -o pid,tid,psr,pcpu,comm --sort=-pcpu | awk 'NR==1 || $3==64'
```

## 超分场景分析

**问题**：1:3 超分时，某个虚拟机 CPU 完全跑满，其他虚拟机是否卡死？

**解答**：配置 shares 字段后，即使一个虚拟机满载，其他虚拟机仍能获得最小保障的 CPU 时间片，但性能会显著下降。

## 相关链接

- [[summaries/perf-tool]]
- [[summaries/linux-irq-interrupt]]
- [[summaries/dfx-tools-overview]]