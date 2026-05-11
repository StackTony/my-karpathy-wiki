---
title: Virtio 核心概念
created: 2026-05-11
updated: 2026-05-11
tags: [virtio, 半虚拟化, IO虚拟化]
sources: [Linux虚拟化/IO虚拟化]
---

## 定义

Virtio 是 Linux 半虚拟化 I/O 的标准框架，通过共享内存和轻量级通知实现高效的虚拟机 I/O。

## 核心组件

| 组件 | 作用 | 位置 |
|------|------|------|
| 前端驱动 | Guest内设备驱动 | virtio-net/blk/scsi |
| Virtqueue | I/O队列抽象 | 共享内存 |
| Vring | Virtqueue实现 | desc + avail + used |
| 后端设备 | Host侧I/O处理 | QEMU/vhost/vDPA |

## 两个核心机制

### 消息通知
- **ioeventfd**: Guest→Host，MMIO写入触发
- **irqfd**: Host→Guest，中断注入

### 数据共享
- **Vring环形缓冲**: 共享内存中的I/O请求队列
- **零拷贝**: 数据直接在共享内存传递

## 性能演进

```
传统virtio(QEMU) → vhost-net(内核) → vhost-user(DPDK) → vDPA(硬件)
     低性能              中性能           高性能           最高性能
```

## 设备类型

| 设备 | 说明 | 显示 |
|------|------|------|
| virtio-net | 网络设备 | eth0 |
| virtio-blk | 块设备 | /dev/vda |
| virtio-scsi | SCSI控制器 | /dev/sda |

## 关键数据结构

```c
// Virtqueue
struct VirtQueue {
    EventNotifier guest_notifier;  // irqfd
    EventNotifier host_notifier;   // ioeventfd
};

// Vring
struct vring {
    vring_desc *desc;   // 描述符表
    vring_avail *avail; // 可用环
    vring_used *used;   // 已用环
};
```

## 参考

- [[summaries/virtio-architecture]]
- [[summaries/virtio-notification-mechanism]]
- [[summaries/virtio-vring-mechanism]]
- [[concepts/ioeventfd-irqfd]]