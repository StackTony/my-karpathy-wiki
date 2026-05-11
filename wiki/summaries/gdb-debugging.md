---
title: GDB 调试指南
created: 2026-05-11
updated: 2026-05-11
tags: [GDB, 调试, QEMU, DFX]
sources: [DFX工具/==gdb调试==]
---

# GDB 调试指南

GDB 是 Linux 下最强大的调试工具，支持 C/C++ 程序和内核调试。

## QEMU 初始化流程调试

由于 QEMU 由 libvirtd 服务拉起且初始化极快，无法直接 attach：

### 调试步骤
```bash
# 1. attach libvirtd 服务
gdb attach $(cat /var/run/libvirtd.pid)

# 2. 在 virCommandSetPreExecHook 打断点 break virCommandSetPreExecHook
(gdb) cont

# 3. 启动虚拟机 virsh start $GUESTNAME

# 4. 触发断点后执行 break main
(gdb) handle SIGKILL nopass noprint nostop
(gdb) handle SIGTERM nopass noprint nostop
(gdb) set follow-fork-mode child
(gdb) cont

# 5. 进入 QEMU main 函数，开始调试
```

## 常用命令速查

### 启动与退出
| 命令 | 说明 |
|------|------|
| `gdb program` | 启动调试 |
| `gdb -q program` | 启动时不显示提示 |
| `quit` | 退出 |
| `run [args]` | 运行程序 |
| `start` | 运行到 main |

### 断点管理
| 命令 | 说明 |
|------|------|
| `break main` | 在 main 设置断点 |
| `break file.c:10` | 在第 10 行设置断点 |
| `break *0x400500` | 在地址设置断点 |
| `tbreak ...` | 临时断点（触发后删除） |
| `info breakpoints` | 查看所有断点 |
| `delete 1` | 删除 1 号断点 |
| `disable/enable 1` | 禁用/启用断点 |

### 条件断点
```bash
break 10 if i==101       # 条件断点
condition 1 i==200       # 修改条件
condition 1              # 删除条件
ignore 1 10              # 忽略前 10 次触发
```

### 观察点 (Watchpoint)
| 命令 | 说明 |
|------|------|
| `watch var` | 写入时停止 |
| `rwatch var` | 读取时停止 |
| `awatch var` | 读写时停止 |

### 执行控制
| 命令 | 说明 |
|------|------|
| `next` (n) | 执行下一行，不进入函数 |
| `step` (s) | 执行下一行，进入函数 |
| `continue` (c) | 继续执行 |
| `finish` | 执行到函数返回 |
| `until` | 执行到循环结束 |

### 打印与显示
| 命令 | 说明 |
|------|------|
| `print var` | 打印变量 |
| `print *ptr` | 打印指针指向值 |
| `print arr[0]@10` | 打印数组前 10 个元素 |
| `print/x var` | 十六进制打印 |
| `ptype var` | 打印变量类型 |
| `info locals` | 打印局部变量 |
| `info args` | 打印函数参数 |

### 内存打印 (examine)
```bash
x/nfu addr    # n=数量, f=格式, u=单位
x/16xb arr    # 16 个字节十六进制
x/16tb arr    # 16 个字节二进制
x/s str       # 打印字符串
```

### 堆栈与帧
| 命令 | 说明 |
|------|------|
| `backtrace` (bt) | 显示调用堆栈 |
| `bt full` | 显示完整堆栈和局部变量 |
| `frame 2` | 切换到第 2 层栈帧 |
| `up/down` | 切换栈帧 |
| `info registers` | 显示寄存器值 |

### 多线程调试
```bash
info threads              # 列出所有线程
thread apply all bt       # 打印所有线程堆栈
thread 3                  # 切换到 3 号线程
set scheduler-locking on  # 只允许当前线程运行
```

### 多进程调试
```bash
set follow-fork-mode child   # 跟随子进程
set follow-fork-mode parent  # 跟随父进程
set detach-on-fork off       # 同时调试父子进程
info inferiors               # 查看所有进程
inferior 2                   # 切换进程
```

## TUI 图形界面
| 命令 | 说明 |
|------|------|
| `tui enable` | 进入 TUI 模式 |
| `layout src` | 显示源代码窗口 |
| `layout asm` | 显示汇编窗口 |
| `layout split` | 同时显示源码和汇编 |
| `layout regs` | 显示寄存器窗口 |

快捷键：`Ctrl+X A` 切换 TUI，`Ctrl+X 2` 切换双窗口

## 常见场景

### 调试循环中的特定迭代
```bash
break loop.c:20 if i==100
```

### 追踪变量何时被修改
```bash
watch global_var
```

### 调试多线程死锁
```bash
thread apply all bt
set scheduler-locking on
```

### 分析 core 文件
```bash
gdb ./program core.12345
bt full
```

## 参考资源

- GDB 官方手册: https://sourceware.org/gdb/onlinedocs/gdb/
- 100 个 GDB 调试技巧: https://github.com/hellogcc/100-gdb-tips
- GDB Dashboard: https://github.com/cyrus-and/gdb-dashboard

## 相关链接

- [[summaries/vmcore-analysis]]
- [[summaries/dfx-tools-overview]]