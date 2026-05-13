---
title: vmcore 解析指南
created: 2026-05-11
updated: 2026-05-13
tags: [vmcore, crash, 内核调试, DFX]
source_dir: DFX工具/==vmcore解析==
source_files: [vmcore解析.md, 寄存器和地址分布.md, 开源crash网站.md, 调度sched.md, 进程结构task_struct和mm_struct.md]
---

# vmcore 解析指南

vmcore（内核崩溃转储）解析是诊断 Linux 内核崩溃问题的关键技术。

## crash 工具基础

### 启动分析
```bash
./crash vmcore vmlinux
```

### 安装 debuginfo
安装 kernel-debuginfo 后，`bt -slf` 可显示：
- 函数偏移
- 函数所在文件
- 每一帧的具体内容

### 时间戳转换
```bash
date -d@'xxxxx' "+%Y-%m-%d %H:%M:%S"
```

## 常用命令

| 命令 | 说明 |
|------|------|
| `ps, files, mount, net` | 输出结构体地址，可配合 `struct` 查看 |
| `foreach bt -c <cpu_id>` | 查看所有进程的堆栈 |
| `bt -a | grep -i COMMAND | grep -v "PID: 0"` | 查看问题堆栈 |
| `dis -s + <address>` | 分析源码中函数来源 |
| `dis -r + <address>` | 反汇编代码流程 |
| `rd -S +<address> + <len>` | 查看内存并尝试解析符号 |
| `struct + task_struct + <address>` | 查看进程结构体信息 |
| `kmem -p + <address>` | 查看内存信息 |

## bt 堆栈字段解析

```
PID: 1234    TASK: ffff880123456000   CPU: 2    COMMAND: "nginx"
 #0 [ffff880123456000] machine_kexec at ffffffff8105c7bb
```

| 字段 | 含义 |
|------|------|
| PID | 进程 ID |
| TASK | task_struct 结构体地址 |
| CPU | 崩溃时运行的 CPU 核心 |
| COMMAND | 进程名称 |
| #N | 栈帧编号（#0 最内层） |
| [addr] | 栈指针地址 |
| function + offset | 函数名及偏移 |

## x86_64 寄存器

### 通用寄存器
| 寄存器 | 用途 |
|--------|------|
| RAX | 函数返回值、算术运算 |
| RBX | 通用寄存器，callee-saved |
| RCX | 循环计数、第4个参数 |
| RDX | 算术运算、第3个参数 |
| RSI | 字符串操作源、第2个参数 |
| RDI | 字符串操作目标、**第1个参数** |
| RBP | **栈帧基址** |
| RSP | **栈指针** |
| RIP | **下一条指令地址**（崩溃分析关键） |

### 函数调用约定
| 参数位置 | 寄存器 |
|----------|--------|
| 第1-6参数 | RDI, RSI, RDX, RCX, R8, R9 |
| 返回值 | RAX |
| 更多参数 | 通过栈传递 |

## ARM64 寄存器

### 通用寄存器
| 寄存器 | 用途 |
|--------|------|
| X0-X7 | **参数寄存器**，X0 也是返回值 |
| X29/FP | 栈帧基址 |
| X30/LR | **返回地址** |
| SP | 栈指针 |
| PC | 程序计数器（= x86 RIP） |

### 异常级别
| EL | 用途 |
|----|------|
| EL0 | 用户态应用程序 |
| EL1 | **内核态** |
| EL2 | 虚拟化，Hypervisor |
| EL3 | Secure Monitor |

## 常见错误分析

### 空指针解引用
```
BUG: unable to handle kernel NULL pointer dereference at 0000000000000123
```
- 地址为 `0x123` → 结构体成员偏移 `0x123` 处，指针本身为 NULL

### 非法地址访问
```
BUG: unable to handle kernel paging request at ffffdeadbeef0000
```
- 地址像"毒值"（如 `0xdeadbeef`）→ 可能是 UAF（使用后释放）

### 栈溢出
```
stack segment: 0000 [#1] SMP
```
- SP 指向非法地址，检查无限递归或局部变量过大

## 进程结构体关系

### task_struct → mm_struct
```
task_struct (进程描述符)
├── 进程标识: pid, tgid, comm
├── 调度信息: state, priority
├── 内存管理: mm, active_mm ← 关键关系
├── 文件系统: files, fs
└── 信号处理: signal
```

### mm_struct 结构
```
mm_struct (内存描述符)
├── mmap: VMA 链表头
├── mm_rb: VMA 红黑树根
├── pgd: 页全局目录
├── start_code/end_code: 代码段
├── start_data/end_data: 数据段
├── start_brk/brk: 堆
├── start_stack: 栈
```

### 页表关系
```
mm_struct → pgd → pmd → pte → 物理页框
```

## 资源链接

- 开源 bugs 搜索: https://bugs.launchpad.net/
- crash 工具包: http://euleros.huawei.com/debug/
- 汇编参考: https://blog.csdn.net/hzj_001/article/details/99703510

## 注意事项

⚠️ 硬件关闭 **SPCR 开关**会导致无法记录硬件日志

## 相关链接

- [[summaries/gdb-debugging]]
- [[summaries/dfx-tools-overview]]
- [[concepts/linux-spinlock]]（mutex crash 解析）