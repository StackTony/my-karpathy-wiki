---
title: VGIC 中断虚拟化
created: 2026-05-12
updated: 2026-05-13
tags: [vgic, 中断虚拟化, GIC, ARM]
source_dir: Linux虚拟化/中断虚拟化
source_files: [vgic中断虚拟化介绍.md]
---

# VGIC 中断虚拟化

Virtual GIC（VGIC）是ARM架构中断虚拟化的核心组件。

## 中断虚拟化场景

| 场景 | 说明 |
|------|------|
| 物理设备中断 | 物理设备产生中断信号，路由到vCPU |
| 虚拟外设中断 | QEMU模拟设备产生中断信号，路由到vCPU |
| IPI核间中断 | Guest OS中CPU间产生中断信号 |

## GIC架构模式

| 模式 | 说明 |
|------|------|
| GICV2 | 传统中断控制器，支持中断虚拟化 |
| GICV3/V4 | 支持更多vCPU、更高效的中断路由 |

## NON-VHE vs VHE

| 模式 | 说明 |
|------|------|
| NON-VHE | 传统模式，EL2和EL1分开 |
| VHE | Virtualization Host Extensions，EL2运行Host内核 |

## VGIC核心组件

- Distributor：中断分发
- CPU Interface：CPU接口
- Virtual CPU Interface：虚拟CPU接口

## 相关链接

- [[summaries/kvm-interrupt-injection]]
- [[summaries/linux-irq-interrupt]]