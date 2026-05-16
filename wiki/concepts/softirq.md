---
title: 软中断 softirq
created: 2026-05-16
updated: 2026-05-16
tags: [Linux, softirq, 中断, 内核]
---

# 软中断 softirq

软中断是 Linux 内核处理中断下半部的核心机制，用于延迟处理硬中断未完成的工作。

---

## 定义

| 特性 | 说明 |
|------|------|
| **执行上下文** | 软中断上下文（softirq context） |
| **中断状态** | 开中断执行（可响应新中断） |
| **优先级** | 高于普通进程，低于硬中断 |
| **机制类型** | 静态机制，编译时确定 |

---

## 与硬中断的区别

| 特性 | 硬中断（IRQ） | 软中断（softirq） |
|------|--------------|------------------|
| 触发源 | 硬件设备 | 内核软件触发 |
| 执行时机 | 立即响应 | 延迟执行 |
| 中断状态 | 关中断 | 开中断 |
| 执行环境 | 中断上下文 | 软中断上下文 |
| 处理内容 | 硬件紧密相关 | 延迟处理逻辑 |

---

## ksoftirqd 内核线程

每个 CPU 对应一个 ksoftirqd 线程：

```
ksoftirqd/0  → CPU 0
ksoftirqd/1  → CPU 1
ksoftirqd/2  → CPU 2
...
```

**作用**：当软中断负载过高时接管处理，避免影响用户进程。

---

## 10 种软中断类型

| 类型 | 用途 | 常见场景 |
|------|------|----------|
| HI_SOFTIRQ | 高优先级tasklet | |
| TIMER_SOFTIRQ | 定时器回调 | 时钟事件 |
| NET_TX_SOFTIRQ | 网络发送 | 协议栈TX |
| NET_RX_SOFTIRQ | 网络接收 | 协议栈RX（高频） |
| BLOCK_SOFTIRQ | 块设备IO | 磁盘读写 |
| TASKLET_SOFTIRQ | tasklet | 延迟任务 |
| SCHED_SOFTIRQ | 调度 | 负载均衡 |
| HRTIMER_SOFTIRQ | 高精度定时器 | |
| RCU_SOFTIRQ | RCU锁 | 读拷贝更新 |

---

## 查看命令

```bash
# 软中断统计
cat /proc/softirqs

# 查看 ksoftirqd 线程
ps aux | grep ksoftirqd

# CPU 软中断开销（top 中的 si）
top -n1 | head -n3
```

---

## 相关链接

- [[summaries/linux-softirq-mechanism]] - 软中断机制详解
- [[summaries/linux-irq-interrupt]] - IRQ 硬中断基础
- [[concepts/linux-rcu]] - RCU 软中断
- [[summaries/linux-network-stack]] - NET_RX/NET_TX
- [[summaries/interrupt-monitoring]] - 中断监控