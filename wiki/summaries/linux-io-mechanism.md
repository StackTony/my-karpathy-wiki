---
title: Linux IO机制全景
created: 2026-05-11
updated: 2026-05-11
tags: [io, storage, kernel, block, scsi]
sources: [Linux IO全景介绍, Linux IO调度算法]
---

## 概述

Linux IO栈是从VFS到磁盘硬件的完整数据路径，涉及Block层、SCSI层和驱动层。

## IO栈架构

```
┌─────────────────────────────────────┐
│           VFS层                      │
│    (Virtual File System)            │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│         Block层                      │
│    (通用Block层 + IO调度)            │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│          SCSI层                      │
│    (SCSI协议处理)                    │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│          驱动层                      │
│    (硬件驱动)                        │
└──────────────────┬──────────────────┘
                   │
┌──────────────────▼──────────────────┐
│          硬件                        │
│    (磁盘/阵列)                       │
└─────────────────────────────────────┘
```

## IO下发流程

### 简易流程

```
Block层排队 → SCSI层提交 → 驱动等待 → 硬件返回
```

### 详细流程

1. **Block层**：请求排队，进入调度
2. **SCSI层**：提交给驱动层等待返回
3. **硬件返回**：处理成功或超时

## 磁盘设备上报流程

```
硬件枚举
    │
    ▼
pci_device_probe()        # PCI驱动匹配
    │
    ▼
xxx_probe()               # 存储驱动probe (ahci_init_one/nvme_probe)
    │
    ▼
控制器初始化 + 设备发现
    │
    ▼
xxx_scan()                # 扫描设备 (scsi_scan_host/nvme_scan_ns)
    │
    ▼
设备识别                  # 获取设备信息
    │
    ▼
块设备注册
    ├── alloc_disk()      # 分配gendisk
    ├── 设置容量、队列、操作函数
    └── add_disk()        # 注册到内核
    │
    ▼
device_add() + kobject_uevent()  # 发送到用户空间
    │
    ▼
用户空间可见: /dev/sda, /sys/block/sda
```

## 缓存模式

| 模式 | 说明 |
|------|------|
| Write Through | 直接与磁盘交互，不利用阵列Cache |
| Write Back | 利用阵列Cache作为二传手，性能更好 |

检查命令：
```bash
cat /sys/class/scsi_disk/0\:2\:0\:0/cache_type
```

## IO调度算法

参见：[IO调度算法介绍](https://cloud.tencent.com/developer/article/1615744)

## 相关链接

- [[virtio整体介绍]]
- [[concepts/linux-io]]