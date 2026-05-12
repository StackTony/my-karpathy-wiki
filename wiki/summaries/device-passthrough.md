---
title: 设备直通 IOMMU/SRIOV/VFIO/vDPA
created: 2026-05-12
updated: 2026-05-12
tags: [iommu, vfio, sriov, vdpa, 设备直通]
sources: [Linux虚拟化/IO虚拟化]
---

# 设备直通机制

设备直通允许虚拟机直接访问物理硬件，绕过虚拟化层提升性能。

## 直通方式对比

| 方式 | 说明 | 特点 |
|------|------|------|
| VFIO | 主机物理设备直接给VM使用 | 主机侧看不到设备，VM独占 |
| SR-IOV | 物理设备虚拟出多个VF | 多VM共享，每个VF独立 |
| vDPA | vDPA设备直通 | 兼容virtio接口 |

## IOMMU

**功能**：设备地址翻译，隔离保护

- DMA地址翻译（设备→内存）
- 设备隔离（防止恶意设备访问）
- 支持设备直通安全

## SR-IOV

**Single Root I/O Virtualization**

| 概念 | 说明 |
|------|------|
| PF | Physical Function，物理设备管理 |
| VF | Virtual Function，虚拟设备实例 |

**优势**：
- 多VM共享同一物理设备
- 每个VF有独立配置空间
- 网卡场景广泛应用

## VFIO

**Virtual Function I/O**

Linux内核框架，安全地将设备直通给用户空间（QEMU）。

```
VFIO框架:
├── IOMMU管理
├── 设备分组
└── 用户空间接口
```

## vDPA

**vData Path Acceleration**

- 兼容virtio接口的硬件设备
- 数据路径硬件加速
- 控制路径virtio协议

## 应用场景

| 场景 | 推荐方式 |
|------|----------|
| 网卡高性能 | SR-IOV VF |
| 网卡独占 | VFIO直通 |
| 存储设备 | VFIO直通 |
| virtio兼容硬件 | vDPA |

## 相关链接

- [[summaries/virtio-architecture]]
- [[summaries/virtio-device-types]]