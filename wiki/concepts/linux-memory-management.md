---
title: Linux 内存管理
created: 2026-05-12
updated: 2026-05-12
tags: [Linux, 内存管理, meminfo]
---

# Linux 内存管理

Linux内核内存管理涉及多个分配接口和统计方式。

## 内存分配接口

| 接口 | 单位 | 统计 |
|------|------|------|
| alloc_pages | 页 | 可能未统计（黑洞） |
| vmalloc | 字节 | VmallocUsed |
| kmalloc | 字节 | Slab |

## 内存分类

| 类别 | 内容 |
|------|------|
| Kernel内存 | Slab、VmallocUsed、PageTables、KernelStack |
| 用户内存 | AnonPages、Mapped、Shmem |
| Page Cache | Cached（文件缓存） |

## 关键结构

| 结构 | 说明 |
|------|------|
| mem_map[] | 页描述符数组 |
| Page Table | 虚拟地址翻译 |
| LRU lists | 页面回收算法 |

## 相关链接

- [[summaries/linux-meminfo]]
- [[summaries/memory-analysis-tools]]