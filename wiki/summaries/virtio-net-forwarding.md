---
title: Virtio-net 内核态网络转发
created: 2026-05-12
updated: 2026-05-12
tags: [virtio, 网络, 内核态, 转发]
sources: [Linux虚拟化/网络虚拟化]
---

# Virtio-net 内核态网络转发

virtio-net设备的内核态数据转发流程。

## 转发流程图

![[Pasted image 20260424161721.png]]

## 核心机制

- 前端驱动（Guest）→ 后端处理（Host）
- vring数据共享
- ioeventfd/irqfd通知机制

## 数据路径

```
Guest发送:
├── 前端填充tx vring
├── ioeventfd通知后端
├── 后端从vring获取数据
├── 内核网络栈处理
└── 发出物理网络

Guest接收:
├── 物理网络接收
├── 后端填充rx vring
├── irqfd注入中断
└── 前端从vring获取数据
```

## 相关链接

- [[summaries/virtio-architecture]]
- [[summaries/virtio-notification-mechanism]]
- [[summaries/virtio-vring-mechanism]]