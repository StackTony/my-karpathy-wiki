---
title: KVM 中断注入机制
created: 2026-05-11
updated: 2026-05-13
tags: [KVM, 中断注入, VGIC, 虚拟化]
source_dir: Linux虚拟化/中断虚拟化
source_files: [KVM中断注入机制.md]
---

# KVM 中断注入机制

KVM 中断虚拟化将中断信号路由到 vCPU，支持物理中断、虚拟中断和 IPI。

## 三种中断来源

| 来源 | 说明 | 路径 |
|------|------|------|
| 物理设备 | 真实硬件中断 | 硬件→Host→KVM→vCPU |
| 虚拟外设 | QEMU模拟 | QEMU→irqfd→KVM→vCPU |
| IPI | Guest核间中断 | Guest→KVM→其他vCPU |

## 中断路由流程

```
中断信号产生
    ↓
kvm_set_irq() → 查找中断路由表
    ↓
kvm_irq_map_gsi() → 获取路由项
    ↓
根据路由类型:
├── KVM_IRQ_ROUTING_IRQCHIP → PIC/IOAPIC
├── KVM_IRQ_ROUTING_MSI → MSI注入
├── KVM_IRQ_ROUTING_MSIX → MSI-X注入
    ↓
架构相关注入函数
    ↓
设置vCPU中断标志 → 注入到vCPU
```

## VGIC（ARM）

### GICV2 中断虚拟化
- LAPIC: 本地高级可编程中断控制器
- IOAPIC: I/O高级可编程中断控制器

### VHE vs NON-VHE
- VHE: 虚拟化扩展，Host运行在EL2
- NON-VHE: 传统模式，需要额外切换

### IPI（核间中断）
- 通过ICR寄存器发起
- 软件写入ICR寄存器信息
- 触发核间中断

## irqfd 中断注入

```
QEMU: event_notifier_set(&vq->guest_notifier)
    ↓ (eventfd写入)
KVM: irqfd_wakeup() → 等待队列回调
    ↓
schedule_work(&irqfd->inject)
    ↓
irqfd_inject() → kvm_set_irq()
    ↓
查找路由表 → 注入到vCPU
```

### 原子注入优化
```c
// 尝试原子注入，避免调度延迟
kvm_arch_set_irq_inatomic(&irq, kvm, ...)
// 失败则使用工作队列
if (ret == -EWOULDBLOCK)
    schedule_work(&irqfd->inject);
```

## 中断路由表

```c
struct kvm_kernel_irq_routing_entry {
    u32 gsi;        // 全局中断号
    u32 type;       // 路由类型
    // 类型相关字段...
};
```

## 性能优化

| 优化 | 说明 |
|------|------|
| irqfd零拷贝 | 无需VM Exit，KVM直接注入 |
| 原子注入 | 避免工作队列调度延迟 |
| 路由表缓存 | 快速查找中断路由 |

## 相关链接

- [[summaries/virtio-notification-mechanism]]
- [[summaries/vgic-interrupt-virtualization]] - VGIC 中断虚拟化
- [[summaries/linux-irq-interrupt]]
- [[concepts/kvm-virtualization]]