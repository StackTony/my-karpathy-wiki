---
title: bpftrace 动态追踪工具
created: 2026-05-16
updated: 2026-05-16
tags: [bpftrace, BPF, 动态追踪, DFX, Linux]
---

# bpftrace 动态追踪工具

bpftrace 是基于 eBPF 的高级动态追踪工具，用于 Linux 内核和用户态程序的性能分析。

---

## 定位

| 特性 | 说明 |
|------|------|
| **技术基础** | eBPF（Extended Berkeley Packet Filter） |
| **执行位置** | 内核态，安全沙箱 |
| **编程语言** | 类 awk 的简洁语法 |
| **适用场景** | 内核函数追踪、用户态探针、性能分析 |

---

## 与其他工具对比

| 工具 | 原理 | 特点 |
|------|------|------|
| **ftrace** | 内核函数追踪 | 内核内置，简单但功能有限 |
| **kprobe** | 动态插桩 | 需编写内核模块 |
| **bpftrace** | eBPF + 简洁语法 | 高级语法、安全、可编程 |

---

## 常用示例

### 追踪内核函数调用次数

```bash
# 追踪 do_sys_open 调用次数
bpftrace -e 'kprobe:do_sys_open { @opens = count(); }'
```

### 追踪函数调用延迟

```bash
# 追踪 vfs_read 执行时间分布
bpftrace -e 'kprobe:vfs_read { @start[tid] = nsecs(); }
kretprobe:vfs_read /@start[tid]/ { @latency = hist(nsecs() - @start[tid]); delete(@start[tid]); }'
```

### 按进程统计调用

```bash
# 按进程统计 open 调用
bpftrace -e 'kprobe:do_sys_open { @opens[comm] = count(); }'
```

---

## 核心概念

| 控制点 | 说明 |
|--------|------|
| **kprobe** | 内核函数入口插桩 |
| **kretprobe** | 内核函数返回插桩 |
| **uprobe** | 用户态函数插桩 |
| **uretprobe** | 用户态函数返回插桩 |
| **tracepoint** | 内核静态追踪点 |

---

## 内置函数

| 函数 | 说明 |
|------|------|
| `count()` | 计数 |
| `sum()` | 求和 |
| `avg()` | 平均值 |
| `hist()` | 直方图分布 |
| `lhist()` | 线性直方图 |
| `printf()` | 格式化输出 |
| `nsecs()` | 纳秒时间戳 |

---

## 相关链接

- [[summaries/ftrace-kprobe-tools]] - ftrace 与 kprobe 追踪
- [[summaries/perf-tool]] - perf 性能分析
- [[summaries/dfx-tools-overview]] - DFX 工具总览