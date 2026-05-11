---
title: ioeventfd 与 irqfd 机制
created: 2026-05-11
updated: 2026-05-11
tags: [ioeventfd, irqfd, eventfd, KVM, 通知机制]
sources: [Linux虚拟化/IO虚拟化]
---

## 定义

ioeventfd 和 irqfd 是 KVM 实现零拷贝通知的核心机制，用于 Virtio 前后端高效通信。

## ioeventfd（Guest→Host）

### 工作原理
```
Guest写MMIO地址 → KVM捕获 → eventfd_signal → 返回Guest → QEMU处理
```

### 关键特点
- **零VM Exit**: KVM处理后立即返回Guest
- **地址匹配**: 注册特定MMIO地址触发
- **异步处理**: QEMU用户态线程处理I/O

### 注册流程
```
QEMU分配eventfd → 加入KVM数组 → 构造ioeventfd → KVM注册虚拟设备write方法
```

## irqfd（Host→Guest）

### 工作原理
```
QEMU写eventfd → KVM等待队列唤醒 → kvm_set_irq() → 中断路由 → 注入vCPU
```

### 关键特点
- **零VM Exit**: KVM内核直接注入中断
- **原子注入**: `kvm_arch_set_irq_inatomic()`尝试原子操作
- **路由表**: 根据GSI查找中断路由

### 数据结构
```c
struct kvm_irqfd {
    int fd;         // eventfd文件描述符
    int gsi;        // 虚拟中断号(GSI)
    unsigned flags;
};
```

## 性能对比

| 方式 | VM Exit次数 | 性能 |
|------|-------------|------|
| 传统通知 | 每次I/O | 低 |
| ioeventfd/irqfd | 0次 | 高 |

## 通知抑制

| 标志 | 说明 |
|------|------|
| VRING_AVAIL_F_NO_INTERRUPT | Guest抑制通知 |
| VIRTIO_RING_F_EVENT_IDX | 精确事件索引 |

## 参考

- [[summaries/virtio-notification-mechanism]]
- [[concepts/virtio]]
- [[summaries/kvm-interrupt-injection]]