---
title: Linux Meminfo 内存统计详解
created: 2026-05-12
updated: 2026-05-12
tags: [Linux, 内存管理, meminfo, /proc]
sources: [Linux操作系统/Linux内存管理]
---

# Linux Meminfo 内存统计详解

/proc/meminfo 是了解Linux系统内存使用状况的主要接口。

## 内存分布总览

![[0364f882842e5e2cfb2f7ab60888f05e.jpeg]]

## 核心统计值

### 基础内存

| 字段 | 说明 |
|------|------|
| MemTotal | 系统可用总内存（扣除BIOS/内核保留） |
| MemFree | 未使用的空闲内存 |
| MemAvailable | 可用内存估算值（含可回收部分） |
| Buffers | 块设备缓存页 |
| Cached | 文件缓存页（page cache） |

### 内核内存

| 字段 | 说明 |
|------|------|
| Slab | Slab分配器内存 |
| SReclaimable | Slab可回收部分 |
| SUnreclaim | Slab不可回收部分 |
| VmallocUsed | vmalloc分配内存 |
| PageTables | 页表占用内存 |
| KernelStack | 内核栈（每个线程8K/16K） |
| HardwareCorrupted | 硬件故障删除的内存页 |

### 用户进程内存

| 字段 | 说明 |
|------|------|
| AnonPages | 匿名页（堆、栈等） |
| Mapped | mapped文件页（Cached子集） |
| Active/Inactive | LRU链表上的页面 |
| Shmem | 共享内存+tmpfs |

### 大页

| 字段 | 说明 |
|------|------|
| HugePages_Total | 预分配大页总数 |
| HugePages_Free | 未使用大页数 |
| HugePages_Rsvd | 预留但未实际使用的大页 |
| AnonHugePages | THP透明大页（与HugePages不同） |

## 内存黑洞问题

Kernel动态内存分配接口：
```
alloc_pages/__get_free_page → 以页为单位（可能未统计）
vmalloc → 以字节为单位（统计在VmallocUsed）
kmalloc → slab基础（统计在Slab）
```

**alloc_pages 分配的内存可能未纳入统计**，形成"内存黑洞"。

## 重要关系式

### LRU链表
```
LRU_INACTIVE_ANON → Inactive(anon)
LRU_ACTIVE_ANON → Active(anon)
LRU_INACTIVE_FILE → Inactive(file)
LRU_ACTIVE_FILE → Active(file)
```

### 内存关系
```
Cached = Mapped + unmapped pages
Cached 包含 Shmem（shared memory + tmpfs）
SwapCached 与 Cached 不重叠

Active(anon) + Inactive(anon) ≈ AnonPages + Shmem
Active(file) + Inactive(file) + Shmem ≈ Cached + Buffers
```

### 进程内存
```
所有进程PSS之和 ≈ Mapped + AnonPages

用户进程内存 = ΣPss + (Cached-Mapped) + Buffers + HugePages
```

## 内存统计公式

```
MemTotal = MemFree + Kernel内存 + 用户进程内存

Kernel内存 = Slab + VmallocUsed + PageTables + KernelStack + HardwareCorrupted + Bounce + 黑洞(X)

用户进程内存 = Active + Inactive + Unevictable + HugePages
```

## 关键区别

| 对比项 | HugePages | THP (AnonHugePages) |
|--------|-----------|---------------------|
| 统计 | 独立统计 | 与AnonPages重叠 |
| RSS/PSS | 不计入 | 计入 |
| LRU | 不在LRU | 在LRU |

## 相关链接

- [[concepts/linux-memory-management]]
- [[summaries/memory-analysis-tools]]