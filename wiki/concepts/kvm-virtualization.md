---
title: KVM 虚拟化性能指标
created: 2026-05-11
updated: 2026-05-11
tags: [KVM, 虚拟化, %ST, EXT, 性能]
sources: [DFX工具/==CPU==]
---

## 定义

KVM 虚拟化场景下的关键性能监控指标。

## %ST (Steal Time)

### 含义
虚拟机等待宿主机 CPU 资源的时间占比，代表被 Hypervisor "偷去"给其他虚拟机的 CPU 时间。

### 来源对比
| 来源 | 关注对象 | 采集方式 |
|------|---------|---------|
| top %ST | 物理机整体 | `/proc/stat` steal 字段差值 |
| kvmtop %ST | 单个虚拟机 | x86: `/var/run/sysinfo/kvmtop/kvmtop_info`<br>arm: `kvmtop -b -n 2 -z` |

### 告警阈值
- **推荐**: > 10% 告警
- **官方**: 连续三周期 ≥ 20% 告警
- **周期**: 1 分钟

## EXT (VM-Exit)

虚拟机退出原因统计：

| EXT 类型 | 说明 | 分析方法 |
|----------|------|---------|
| EXThvc | HVC 指令调用 Hypervisor | 正常 |
| EXTwfe/wfi | WFE/WFI 等待指令 | 关注 halt-polling |
| EXTmmioU/K | MMIO I/O 访问 | IOMMU 相关 |
| EXTfp | 浮点/向量指令 | 浮点运算 |
| **EXTirq** | 核间中断 | 使用 irqtop 分析 |
| **EXTsys64** | 系统调用（SVC） | 应用 read/write |
| EXTmabt | 内存访问异常 | 缺页 |

### EXTirq 分析
```bash
# 虚拟机内查看中断
irqtop
cat /proc/interrupts

# 关注点:
# - IPI_RESCHEDULE
# - IPI_CALL_FUNC
# - virtio 设备中断
```

## CPU 超分场景

### 问题描述
1:3 超分时，某虚拟机 CPU 完全跑满，其他虚拟机是否卡死？

### 解答
配置 shares 字段后：
- 即使一个虚拟机满载，其他虚拟机仍能获得最小保障 CPU 时间片
- 但性能会显著下降

## D 状态线程

### 查找命令
```bash
ps -eL -o pid,tid,psr,state,comm,cmd | grep -E '(KVM|qemu)' | grep "D "
```

### D 状态含义
不可中断睡眠，通常等待 IO，可能导致系统卡顿。

## 参考

- [[summaries/kvmtop-tool]]
- [[summaries/perf-tool]]
- [[summaries/linux-irq-interrupt]]