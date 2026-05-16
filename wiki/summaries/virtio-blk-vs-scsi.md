---
title: Virtio-blk 与 Virtio-scsi 对比
created: 2026-05-16
updated: 2026-05-16
tags: [virtio, 虚拟化, IO虚拟化, virtio-blk, virtio-scsi]
source_dir: Linux 虚拟化/IO虚拟化
source_files: [virtio-blk和virtio-scsi的理解.md]
---

# Virtio-blk 与 Virtio-scsi 对比

Virtio 两种块设备半虚拟化方案的差异与选择。

---

## 核心机制

两种设备都采用相同的 virtio 通信机制：

| 机制 | 方向 | 说明 |
|------|------|------|
| **io_event_fd** | 前端→后端 | IO请求通知 |
| **中断注入** | 后端→前端 | 完成通知 |
| **vring环** | 双向 | 数据共享 |

---

## 设备标识差异

| 驱动类型 | 设备显示 | 说明 |
|----------|----------|------|
| virtio_blk | `/dev/vda` | virtio-blk专用标识 |
| virtio_scsi | `/dev/sda` | SCSI标准标识 |
| IDE硬盘 | `/dev/hda` | 传统IDE标识 |
| SATA硬盘 | `/dev/sda` | 与virtio-scsi相同 |

---

## 扩展能力对比

| 特性 | virtio-blk | virtio-scsi |
|------|------------|-------------|
| **设备数量** | ~30个 | 数百个 |
| **PCI插槽** | 易耗尽 | 不受限 |
| **后端驱动** | virtio_blk | scsi-block |
| **可扩展性** | 低 | 高 |

---

## 架构差异

### virtio-blk

- 简单块设备抽象
- 每个设备占用一个PCI插槽
- 功能：IO请求提交 + 数据传递

### virtio-scsi

- 半虚拟化SCSI控制器
- 单一控制器可管理大量设备
- Linux内核 scsi-block 驱动管理
- 更好的性能和可扩展性

---

## 选择建议

| 场景 | 推荐 |
|------|------|
| 少量磁盘（<30） | virtio-blk（简单） |
| 大量磁盘或LUN | virtio-scsi（可扩展） |
| 生产环境 | virtio-scsi（标准化） |
| 传统虚拟机迁移 | virtio-blk（兼容性） |

---

## 相关链接

- [[summaries/virtio-architecture]] - Virtio整体架构
- [[summaries/virtio-vring-mechanism]] - Vring数据共享机制
- [[summaries/virtio-notification-mechanism]] - 消息通知机制
- [[concepts/virtio]] - Virtio核心概念