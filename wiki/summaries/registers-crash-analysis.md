---
title: 寄存器与崩溃分析
created: 2026-05-16
updated: 2026-05-16
tags: [vmcore, crash, 寄存器, x86_64, ARM64, 崩溃分析]
source_dir: DFX工具/==vmcore解析==
source_files: [寄存器和地址分布.md]
---

# 寄存器与崩溃分析

vmcore 堆栈解析中寄存器字段含义，x86_64 与 ARM64 架构对比。

---

## bt 命令输出堆栈字段解析

```
crash> bt
PID: 1234    TASK: ffff880123456000   CPU: 2    COMMAND: "nginx"
 #0 [ffff880123456000] machine_kexec at ffffffff8105c7bb
```

| 字段 | 含义 |
|------|------|
| **PID** | 进程ID，唯一标识一个进程 |
| **TASK** | `task_struct` 结构体地址，可查看详细信息 |
| **CPU** | 崩溃时进程运行的CPU核心编号 |
| **COMMAND** | 进程名称/命令名 |
| **#N** | 栈帧编号，#0是最内层（崩溃点） |
| **[addr]** | 栈帧的栈指针地址（RSP/RBP 或 SP/FP） |
| **function + offset** | 函数名及其偏移量 |

---

## x86_64 寄存器概览

### 通用寄存器

| 寄存器 | 用途 |
|--------|------|
| **RAX** | 函数返回值、算术运算 |
| **RDI** | 第1个参数 |
| **RSI** | 第2个参数 |
| **RDX** | 第3个参数 |
| **RCX** | 第4个参数 |
| **R8** | 第5个参数 |
| **R9** | 第6个参数 |
| **RBP** | 栈帧基址，定位局部变量 |
| **RSP** | 栈指针，指向栈顶 |
| **RIP** | 下一条指令地址，崩溃分析关键 |

### 函数调用约定 (System V AMD64 ABI)

| 参数位置 | 寄存器 |
|----------|--------|
| 第1-6个参数 | RDI, RSI, RDX, RCX, R8, R9 |
| 更多参数 | 通过栈传递 |
| 返回值 | RAX |

---

## ARM64 (AArch64) 寄存器概览

### 通用寄存器

| 寄存器 | 用途 |
|--------|------|
| **X0-X7** | 参数寄存器，X0也是返回值 |
| **X29 (FP)** | 栈帧基址 |
| **X30 (LR)** | 返回地址 |
| **SP** | 栈指针 |
| **PC** | 程序计数器 |

### 函数调用约定 (AAPCS64)

| 参数位置 | 寄存器 |
|----------|--------|
| 第1-8个参数 | X0-X7 |
| 更多参数 | 通过栈传递 |
| 返回值 | X0 |

### 异常级别

| EL | 用途 |
|----|------|
| **EL0** | 用户态应用程序 |
| **EL1** | 内核态，Linux内核运行级别 |
| **EL2** | 虚拟化，Hypervisor |
| **EL3** | Secure Monitor，ARM TrustZone |

---

## 架构对比速查表

| 功能 | x86_64 | ARM64 |
|------|--------|--------|
| 程序计数器 | RIP | PC |
| 栈指针 | RSP | SP |
| 帧指针 | RBP | X29 (FP) |
| 返回地址 | 栈上保存 | X30 (LR) |
| 第1-6参数 | RDI,RSI,RDX,RCX,R8,R9 | X0-X5 |
| 返回值 | RAX | X0 |
| 内核态级别 | Ring 0 | EL1 |

---

## 常见崩溃场景分析

### 空指针解引用

```
BUG: unable to handle kernel NULL pointer dereference at 0000000000000123
```

- 地址为 `0x123` → 结构体成员偏移处，指针本身为 NULL
- 查看 `bt` 找崩溃函数，检查入参

### 非法地址访问

```
BUG: unable to handle kernel paging request at ffffdeadbeef0000
```

- 地址像"毒值"（如 `0xdeadbeef`）→ 可能是 UAF

### 栈溢出

```
stack segment: 0000 [#1] SMP
```

- SP 指向非法地址，检查递归或局部变量过大

---

## ARM64 栈回溯

栈帧结构：
```
+------------------+ <- 高地址
|  局部变量        |
+------------------+
|  保存的 FP (X29) | <- 当前 FP 指向这里
+------------------+
|  保存的 LR (X30) |
+------------------+ <- 低地址 (SP)
```

回溯过程：`FP → 保存的FP → 保存的LR → 上一级FP → ...`

---

## 相关链接

- [[summaries/vmcore-analysis]] - vmcore崩溃转储解析
- [[concepts/crash-analysis]] - crash工具分析技术
- [[concepts/registers-analysis]] - 寄存器分析技术