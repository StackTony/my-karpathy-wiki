---
title: ftrace 与 kprobe 追踪工具
created: 2026-05-11
updated: 2026-05-13
tags: [ftrace, kprobe, trace, 内核调试, DFX]
source_dir: DFX工具/==设置trace点==
source_files: [1 ftrace和kprobe和bpftrace.md, trace：ftrace使用方法.md, trace：kprobe使用方式.md]
---

# ftrace 与 kprobe 追踪工具

Linux 内核动态追踪工具，用于函数调用链分析和内核行为调试。

## 工具对比

| 工具 | 优点 | 缺点 | 适用场景 |
|------|------|------|---------|
| ftrace | 完整函数调用链、function_graph | 只能追踪静态函数、开销大 | 函数流程追踪 |
| kprobe | 可追踪任意内核函数、灵活 | 需要手动配置 | sched_switch、virtio_notify 等 |

## ftrace 使用方法

### 基本流程
```bash
cd /sys/kernel/debug/tracing/

# 清空缓存
echo > trace

# 关闭追踪
echo 0 > tracing_on

# 设置追踪类型
echo function_graph > current_tracer

# 设置过滤函数
echo cma_alloc > set_graph_function
# 或
echo cma_alloc > set_ftrace_filter

# 开启追踪
echo 1 > tracing_on
```

### 查看结果
```bash
# 总体 trace
cat trace

# 每个 CPU 的 trace
cat per_cpu/cpu0/trace
```

### 关闭清理
```bash
echo 0 > tracing_on
echo > set_ftrace_filter     # 清空过滤器
echo nop > current_tracer    # 恢复默认
```

### 注意事项
如果出现 "Device or resource busy" 错误：
```bash
systemctl stop rasdaemon.service
```

## kprobe 使用方法

### 添加探测点
```bash
# 1. 查看可用函数
cat /sys/kernel/debug/tracing/available_filter_functions

# 2. 添加 kprobe 事件
echo 'p:my_probe queue_work' > /sys/kernel/debug/tracing/kprobe_events

# 3. 开启 kprobe
echo 1 > /sys/kernel/debug/tracing/events/kprobes/enable

# 4. 开启 tracing
echo 1 > /sys/kernel/debug/tracing/tracing_on

# 5. 开启 stacktrace
echo 1 > options/stacktrace
```

### 关闭清理
```bash
echo 0 > /sys/kernel/debug/tracing/events/kprobes/enable
echo 0 > /sys/kernel/debug/tracing/tracing_on
echo '' > /sys/kernel/debug/tracing/kprobe_events
```

## 调度事件追踪

### CPU 单核调度轨迹
```bash
# 开启调度事件
echo 1 > /sys/kernel/debug/tracing/events/sched/sched_wakeup/enable
echo 1 > /sys/kernel/debug/tracing/events/sched/sched_switch/enable

# 过滤特定 CPU
echo 'cpu0 || cpu1 || cpu2' > /sys/kernel/debug/tracing/events/sched/sched_wakeup/filter
echo 'cpu0 || cpu1 || cpu2' > /sys/kernel/debug/tracing/events/sched/sched_switch/filter

# 开启调用堆栈
echo 1 > options/stacktrace
```

## 脚本示例

自动追踪脚本（xxx.sh + 函数名）：
```bash
func=$1
tracepath="/sys/kernel/debug/tracing"

# 关闭已有 kprobe
state=$(cat $tracepath/events/kprobes/enable)
if [[ $state -eq 1 ]]; then
    echo 0 > $tracepath/events/kprobes/enable
fi

# 添加新的探测点
echo "p:$func $func" >> $tracepath/kprobe_events

# 设置事件和选项
echo $func > $tracepath/set_event
echo stacktrace > $tracepath/trace_options
echo 1 > $tracepath/events/kprobes/enable
echo > $tracepath/trace
```

## 参考链接

- ftrace vs kprobe: https://zhuanlan.zhihu.com/p/652907405
- kprobe 详细指南: https://blog.csdn.net/luckyapple1028/article/details/52972315

## 相关链接

- [[summaries/perf-tool]]
- [[summaries/dfx-tools-overview]]
- [[concepts/bpftrace]] - eBPF 高级动态追踪