---
title: Virtio 整体架构
created: 2026-05-11
updated: 2026-05-13
tags: [virtio, 虚拟化, IO, KVM]
source_dir: Linux虚拟化/IO虚拟化
source_files: [virtio整体介绍.md]
---

# Virtio 整体架构

Virtio 是 Linux 虚拟化中半虚拟化 I/O 的标准框架，实现 Guest 与 Host 之间高效的 I/O 数据交互。

## 核心架构

```
前端驱动(Guest) ←── Virtqueue(Vring) ──→ 后端设备(Host)
     ↓                    ↓                    ↓
virtio-net/blk       共享内存环形缓冲        QEMU/vhost/vDPA
```

### 关键机制
1. **消息通知机制**: ioeventfd（Guest→Host）+ irqfd（Host→Guest）
2. **数据共享机制**: Vring 环形缓冲区（desc + avail + used）

## 架构演进对比

| 架构 | 数据面经QEMU | 数据面经KVM | 位置 | 性能 | 硬件要求 |
|------|-------------|-------------|------|------|---------|
| 传统 virtio | ✓ | ✓ | QEMU用户态 | ⭐低 | 无 |
| vhost-net | ✗ | ✓ | 内核态 | ⭐⭐中 | 无 |
| vhost-user | ✗ | ✓ | DPDK用户态 | ⭐⭐⭐高 | 无 |
| vDPA | ✗ | ✓ | 硬件 | ⭐⭐⭐⭐最高 | vDPA硬件 |

## IO 流程（传统 virtio）

```
1. Guest产生IO请求 → virtio驱动 → scatterlist
2. virtqueue_add_buf → 映射到Vring共享区
3. kick通知 → 写PCI配置空间 → VM Exit
4. QEMU监听 → 从Vring获取请求
5. QEMU封装 → 发送硬件
6. 硬件完成 → QEMU更新used ring
7. irqfd注入中断 → Guest处理完成
```

## 相关链接

- [[summaries/virtio-notification-mechanism]]
- [[summaries/virtio-vring-mechanism]]
- [[summaries/virtio-device-types]]
- [[summaries/virtio-device-init]] - 设备初始化流程
- [[summaries/virtio-net-forwarding]] - 内核态网络转发
- [[concepts/kvm-virtualization]]
- [[concepts/virtio]]
- [[summaries/linux-irq-interrupt]]