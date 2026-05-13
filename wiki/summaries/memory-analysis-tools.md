---
title: 内存分析工具
created: 2026-05-11
updated: 2026-05-13
tags: [内存, numastat, slab, DFX]
source_dir: DFX工具/==内存==
source_files: [perf工具分析slab内存占用.md, 各node上进程内存（含qemu）占用情况.md, 查看虚拟机OS预占的内存情况.md]
---

# 内存分析工具

Linux 内存分析工具集，用于监控虚拟机内存占用、NUMA 分布和 slab 内存。

## 虚拟机内存预占分析

### 通过 dmesg 查看
```bash
dmesg | grep Memory
# 输出示例:
# Memory: 3848740k/5242880k available (7792k kernel code, 
#         1049480k absent, 344660k reserved, 5950k data, 1984k init)
```

### 内存计算
```
available = 物理内存 - absent - reserved
```

- **reserved**: kdump 使用的内存，永远不会释放
- **absent**: 不可用内存

## NUMA 内存分布分析

### 查看 NUMA 统计
```bash
numastat -m
```

### 查看进程虚拟内存布局
```bash
cat /proc/pid/maps
# 或
pmap pid
```

### 查看 NUMA 各 node 进程内存占用
```bash
# 所有进程
ps aux | grep -v grep | awk '{print $2}' | xargs numastat -p

# 仅 QEMU 进程
ps aux | grep qemu-kvm | grep -v grep | awk '{print $2}' | xargs numastat -p
```

### 查看大页使用情况
```bash
cat /proc/*/numa_maps | grep -i huge
```

### 确定内存具体使用
```bash
cat /proc/<pid>/numa_maps | grep -w Nxx
```

## 注意事项

> qemu 进程自己（堆、栈、二进制）不受配置文件里的 strict 限制，strict 控制的是用户空间的分配

## Slab 内存分析

### perf kmem 分析
```bash
# 采集 slab 分配和释放情况
perf kmem --alloc --caller record sleep 1

# 显示采集结果
perf kmem --alloc --caller stat
```

用于分析：
- 内存分配热点
- 内存碎片产生位置

## 相关链接

- [[summaries/perf-tool]]
- [[summaries/dfx-tools-overview]]
- [[summaries/vmcore-analysis]]（mm_struct 结构体）