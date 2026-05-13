---
title: 火焰图工具
created: 2026-05-11
updated: 2026-05-13
tags: [火焰图, perf, CPU, 性能分析, DFX]
source_dir: DFX工具/==设置trace点==
source_files: [3 火焰图.md]
---

# 火焰图工具

火焰图（Flame Graph）是 CPU 调用栈的可视化 SVG 图，用于快速定位性能瓶颈。

## 核心概念

- **Y 轴**: 调用栈深度，顶层是正在执行的函数，下方是父函数
- **X 轴**: 抽样数，宽度越大表示执行时间越长（按字母排序合并）
- **颜色**: 无特殊含义，随机配色

## 解析方法

关注"平顶"（plateaus）：
- 平顶 = 函数占据宽度最大
- 可能存在性能问题

## 生成流程

```bash
# 1. perf 采样
perf record -e cpu-clock -g -p $(pidof xxx)
# 或
perf record -C 33-38 -g -a -- sleep 20

# 2. 解析数据
perf script -i perf.data > perf.unfold

# 3. 折叠符号（进入 FlameGraph 目录）
./stackcollapse-perf.pl perf.unfold > perf.folded

# 4. 生成 SVG
./flamegraph.pl perf.folded > perf.svg
```

## FlameGraph 项目

- GitHub: https://github.com/brendangregg/FlameGraph
- 支持: perf、DTrace、SystemTap 等多种数据源

## 典型应用

| 场景 | 命令 |
|------|------|
| 全系统 CPU 分析 | `perf record -g -a -- sleep 30` |
| 特定进程分析 | `perf record -F 99 -p PID -g -- sleep 60` |
| 特定 CPU 核心 | `perf record -C 33-38 -g -a -- sleep 20` |

## 与其他工具配合

火焰图以 perf 为前提：
- perf 提供原始采样数据
- FlameGraph 脚本进行可视化处理

## 相关链接

- [[summaries/perf-tool]]
- [[summaries/dfx-tools-overview]]
- 参考: http://www.cnblogs.com/leonxyzh/p/7346092.html