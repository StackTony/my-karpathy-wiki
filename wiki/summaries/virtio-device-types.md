---
title: Virtio 设备类型
created: 2026-05-11
updated: 2026-05-11
tags: [virtio, virtio-blk, virtio-scsi, virtio-net]
sources: [Linux虚拟化/IO虚拟化]
---

# Virtio 设备类型

Virtio 支持多种虚拟设备类型，覆盖块存储、网络、SCSI等场景。

## virtio-blk

### 特点
- 块设备，显示为 `/dev/vda`
- 采用 ioeventfd 前端→后端通知
- 采用中断注入 后端→前端通知
- 通过 Vring 共享数据

### 设备标识
| 驱动 | 设备显示 | 说明 |
|------|---------|------|
| virtio_blk | /dev/vda | Virtio块设备 |
| IDE | /dev/hda | 传统IDE |
| SATA | /dev/sda | SATA硬盘 |

## virtio-scsi

### 特点
- SCSI控制器设备
- 可处理数百个设备
- virtio-blk仅能处理约30个设备

### 设备标识
| 驱动 | 设备显示 | 说明 |
|------|---------|------|
| scsi-block | /dev/sda | Virtio SCSI |
| virtio-scsi | - | 后端设备 |

### 优势对比

| 特性 | virtio-blk | virtio-scsi |
|------|-----------|-------------|
| 设备数量 | ~30个 | 数百个 |
| PCI插槽 | 易耗尽 | 灵活 |
| 扩展性 | 有限 | 优秀 |

## virtio-net

### 架构演进

| 后端 | 数据面位置 | 性能 | 说明 |
|------|-----------|------|------|
| QEMU | 用户态 | 低 | 原始实现 |
| vhost-net | 内核态 | 中 | 内核驱动 |
| vhost-user | DPDK用户态 | 高 | 轮询模式 |
| vDPA | 硬件 | 最高 | 硬件加速 |

### 数据流向
```
Guest virtio-net驱动 → Vring → 后端 → TAP/OVS → 物理网络
```

## 设备直通

### VFIO + IOMMU
- PF: 主物理设备直接给VM，主机看不到设备
- VF: SR-IOV虚拟出多个VF，配置给VM

### vDPA
- 数据面在硬件直通
- 控制面保持virtio标准接口
- 网卡可直接中断到VM

### 对比

| 方式 | 数据面 | 性能 | 灵活性 |
|------|--------|------|--------|
| virtio | QEMU | 低 | 高 |
| vhost | 内核 | 中 | 中 |
| VFIO直通 | 硬件 | 高 | 低 |
| vDPA | 硬件+virtio接口 | 高 | 高 |

## 相关链接

- [[summaries/virtio-architecture]]
- [[summaries/virtio-notification-mechanism]]
- [[summaries/virtio-vring-mechanism]]
- [[summaries/kvm-live-migration]]