---
title: crash 工具分析技术
created: 2026-05-11
updated: 2026-05-11
tags: [crash, vmcore, 调试, 内核]
sources: [DFX工具/==vmcore解析==]
---

## 定义

crash 是分析 Linux 内核崩溃转储（vmcore）的核心工具，用于定位内核崩溃根因。

## 常用命令

| 命令 | 说明 |
|------|------|
| `ps, files, mount, net` | 输出结构体地址，配合 `struct` 查看 |
| `foreach bt -c <cpu_id>` | 查看指定 CPU 所有进程堆栈 |
| `bt -a \| grep COMMAND \| grep -v "PID: 0"` | 过滤问题堆栈 |
| `dis -s <addr>` | 查看函数源码 |
| `dis -r <addr>` | 反汇编代码流程 |
| `rd -S <addr> <len>` | 查看内存并解析符号 |
| `struct task_struct <addr>` | 查看进程结构体 |
| `kmem -p <addr>` | 查看内存信息 |

## Mutex 解析

### Owner 字段解码

Linux mutex 的 owner 字段低 3 位是状态标志，需要清除后获取真实 task_struct：

```bash
# 查看 mutex 结构
crash> p rtnl_mutex
rtnl_mutex = $1 = {
  owner = {
    counter = -98125497684349,
  },
}

# 解码 owner（清除低 3 位）
crash> struct task_struct FFFFA6C160912E80 | grep -i pid
pid = 774537,
```

### 标志位含义
- `0x01`: MUTEX_FLAG_WAITERS（有等待者）
- `0x02`: MUTEX_FLAG_HANDOFF（移交锁）

## 进程结构体关系

### task_struct → mm_struct

```
task_struct (进程描述符)
├── pid, tgid, comm: 进程标识
├── state, priority: 调度信息
├── mm, active_mm: 内存管理 ← 关键
├── files, fs: 文件系统
└── signal: 信号处理
```

### mm_struct 结构

```
mm_struct → pgd → pmd → pte → 物理页框
├── mmap: VMA 链表
├── mm_rb: VMA 红黑树
├── start_code/end_code: 代码段
├── start_brk/brk: 堆
├── start_stack: 栈
```

### 线程共享

同一线程组共享同一个 `mm_struct`：
- tgid 相同的线程共享地址空间
- 内核线程 mm=NULL，借用 active_mm

## 时间戳转换

```bash
date -d@'timestamp' "+%Y-%m-%d %H:%M:%S"
```

## 参考

- [[summaries/vmcore-analysis]]
- [[concepts/linux-mutex]]（Mutex 解析）