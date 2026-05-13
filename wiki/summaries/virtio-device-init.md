---
title: Virtio 设备初始化流程
created: 2026-05-12
updated: 2026-05-13
tags: [virtio, 设备初始化, qemu]
source_dir: Linux虚拟化/IO虚拟化
source_files: [1）设备初始化流程.md]
---

# Virtio 设备初始化流程

Virtio设备的创建和初始化过程涉及QEMU设备模型。

## QEMU设备模型

```
设备类型注册 → realize → 初始化
```

## virtio-net设备初始化流程

![[Pasted image 20260424165040.png]]

## 核心流程

```
virtio_device_realize:
├── virtio_bus_device_realize
├── virtio_pci_realize (PCI设备)
├── virtio_net_realize (网络设备)
└── 初始化virtqueue
```

## virtio-blk/virtio-scsi区别

| 设备 | 显示名称 | 特点 |
|------|----------|------|
| virtio-blk | /dev/vda | 简单块设备，约30个设备上限 |
| virtio-scsi | /dev/sda | SCSI控制器，支持数百设备 |

## virtio-scsi优势

- 支持大量设备
- SCSI协议完整支持
- driver: scsi-block

## 参考链接

- QEMU设备注册：<https://richardweiyang-2.gitbook.io/understanding_qemu/00-devices/01-type_register>

## 相关链接

- [[summaries/virtio-architecture]]
- [[summaries/virtio-device-types]]