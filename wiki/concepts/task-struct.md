---
title: task_struct 与 mm_struct
created: 2026-05-11
updated: 2026-05-11
tags: [task_struct, mm_struct, 进程, 内核]
sources: [DFX工具/==vmcore解析==]
---

## 定义

Linux 内核进程管理的核心数据结构。

## task_struct（进程描述符）

```
task_struct
├── 进程标识: pid, tgid, comm
├── 调度信息: state, priority
├── 内存管理: mm, active_mm ← 关键
├── 文件系统: files, fs
└── 信号处理: signal
```

### 关键字段
| 字段 | 说明 |
|------|------|
| pid | 进程 ID |
| tgid | 线程组 ID |
| mm | 用户空间内存描述符 |
| active_mm | 内核线程借用 |
| parent | 父进程 |
| children | 子进程链表 |

## mm_struct（内存描述符）

```
mm_struct (虚拟地址空间)
├── mmap: VMA 链表头
├── mm_rb: VMA 红黑树
├── pgd: 页全局目录 ← 页表入口
├── start_code/end_code: 代码段
├── start_data/end_data: 数据段
├── start_brk/brk: 堆
├── start_stack: 栈
├── arg_start: 参数区
├── env_start: 环境变量区
```

### 页表关系
```
mm_struct → pgd → pmd → pte → 物理页框
```

## vm_area_struct (VMA)

虚拟内存区域（内存段）：

```
vm_area_struct
├── vm_start: 区域起始虚拟地址
├── vm_end: 区域结束虚拟地址
├── vm_next: 链表下一个
├── vm_rb: 红黑树节点
├── vm_mm: 所属 mm_struct
├── vm_flags: 权限（读/写/执行）
├── vm_file: 关联文件（映射时）
```

## 多线程共享

```
task_struct (线程1)  task_struct (线程2)  task_struct (线程3)
tid=1001, tgid=1001  tid=1002, tgid=1001  tid=1003, tgid=1001
        └──────────────────┬──────────────────┘
                           │ mm 共享
                           ▼
                   mm_struct (同一地址空间)
                   代码段/数据段/堆/栈共享
```

### 关键点
- 同线程组共享 mm_struct
- 各线程栈独立但在同一空间
- 内核线程 mm=NULL，借用 active_mm

## crash 查看

```bash
# 查看进程结构体
crash> struct task_struct <addr>

# 查看内存描述符
crash> struct mm_struct <addr>

# 查看页表
crash> p <addr>->pgd
```

## 参考

- [[summaries/vmcore-analysis]]
- [[concepts/crash-analysis]]