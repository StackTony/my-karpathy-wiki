---
title: Linux IRQ中断机制
created: 2026-05-11
updated: 2026-05-11
tags: [irq, 中断, kernel]
sources: [Linux IRQ中断]
---

## 概述

Linux IRQ中断机制是内核处理硬件中断的核心系统。

## 核心概念

### 核间中断（IPI）

核间中断（Inter-Processor Interrupt）是多核系统中CPU间通信的关键机制。

**核心寄存器**：ICR（Interrupt Command Register）

软件按照寄存器规则写入信息即可发出IPI。

## 数据流向

```
硬件中断触发 → ICR寄存器 → CPU响应 → 中断处理程序
```

## 参考

- 来源：[腾讯云文章](https://cloud.tencent.com/developer/article/1517862)