---
title: perf 工具详解
created: 2026-05-11
updated: 2026-05-11
tags: [perf, CPU, 性能分析, DFX]
sources: [DFX工具/==CPU==, DFX工具/==设置trace点==]
---

# perf 工具详解

perf 是 Linux 系统原生提供的性能分析工具，基于事件采样原理，支持处理器和操作系统性能事件的剖析。

## 核心原理

perf 以固定间隔在 CPU 上产生中断，采样当前运行的 pid 和函数，统计 CPU 时间分布：
- 运行时间越多的函数，被采样的概率越大
- 从而推测 CPU 占用率分布

## 性能事件类型

| 类型 | 说明 | 示例 |
|------|------|------|
| Hardware Event | PMU 硬件产生 | cache-misses, cycles |
| Software Event | 内核软件产生 | context-switches, page-faults |
| Tracepoint Event | 内核静态 tracepoint | sched_switch, irq_entry |

## 常用子工具

### perf stat - 全局监控
```bash
perf stat -p PID -- sleep 10
```
关键指标：
- task-clock: 任务时钟周期
- context-switches: 上下文切换次数
- cpu-migrations: CPU 迁移次数
- page-faults: 页错误次数

### perf top - 实时热点
```bash
perf top -e cycles -p PID
```
参数：
- `-e <event>`: 指定性能事件
- `-p <pid>`: 仅分析目标进程
- `-g`: 显示调用关系图

### perf record/report - 采样分析
```bash
# CPU 使用率采样
perf record -C 64-67 -g -- sleep 10
perf report --no-ch

# CPU 调度轨迹
perf sched record -g -p PID sleep 10
perf sched script > cpu_sched.log

# 虚拟机性能事件
perf kvm stat record -p PID -- sleep 10
perf kvm stat report
```

### perf kmem - 内存分析
```bash
# slab 内存分析
perf kmem --alloc --caller record sleep 1
perf kmem --alloc --caller stat
```

## CPU 分析场景

### 火焰图生成
```bash
perf record -F 99 -p PID -g -- sleep 60
perf script > out.perf
/opt/FlameGraph/stackcollapse-perf.pl out.perf > out.folded
/opt/FlameGraph/flamegraph.pl out.folded > cpu.svg
```

### 单核 CPU 进程调度
```bash
# 查看特定 CPU 上运行的进程
ps -eL -o pid,tid,psr,pcpu,comm --sort=-pcpu | awk 'NR==1 || $3==64'
```

### D 状态线程查找
```bash
# 找出 QEMU 中处于 D 状态的 vcpu/线程
ps -eL -o pid,tid,psr,state,comm,cmd | grep -E '(KVM|qemu)' | grep "D "
```

## 虚拟化场景

### %ST (Steal Time) 分析
- **采集原理**: `/proc/stat` 中 steal 字段差值 / 总 CPU 差值
- **top %ST**: 整个物理机的 CPU 抢占情况
- **kvmtop %ST**: 虚拟机的 CPU 抢占情况
- **阈值**: > 10% 建议告警，> 20% 连续三周期告警

## 相关概念

- [[summaries/flamegraph-tool]]
- [[summaries/ftrace-kprobe-tools]]
- [[summaries/dfx-tools-overview]]