---
title: Vring 环形缓冲区
created: 2026-05-11
updated: 2026-05-11
tags: [vring, 共享内存, ring-buffer, Virtio]
sources: [Linux虚拟化/IO虚拟化]
---

## 定义

Vring 是 Virtio 数据共享机制的核心，通过三个环形表实现前后端零拷贝数据交换。

## 三个核心表

### vring_desc（描述符表）
```
存储IO请求的GPA地址和链表结构
├── addr: GPA地址
├── len: 数据长度
├── flags: NEXT/WRITE标志
└── next: 链表下一项索引
```

### vring_avail（可用环）
```
前端→后端: 指明哪些desc链表可用
├── flags: 标志位
├── idx: 下一个可用位置
└── ring[]: desc链表头位置
```

### vring_used（已用环）
```
后端→前端: 指明哪些desc链表已处理
├── flags: 标志位
├── idx: 下一个可用位置
└── ring[].id: desc链表头
    ring[].len: 链表长度
```

## 生产者-消费者模型

| 表 | 前端角色 | 后端角色 |
|----|---------|---------|
| avail | 生产者（写入） | 消费者（读取） |
| used | 消费者（读取） | 生产者（写入） |
| desc | 生产者（写入） | 消费者（读取） |

## Scatter-Gather 链表

一个IO请求可能占用多个desc项：
```
desc[0] → next=1 → desc[1] → next=2 → desc[2] → next=NULL
                                        ↑ flags=~NEXT（结束）
```

## 操作流程

### virtqueue_add_buf
```
1. 找空闲desc项
2. 存IO请求GPA地址
3. 设置flags和next组成链表
4. 链表头写入avail->ring[idx]
5. idx++
```

### virtqueue_get_buf
```
1. 从avail读取链表头
2. 按next遍历desc链表
3. 处理IO请求
4. 链表头写入used->ring[idx]
5. idx++
```

## 内存布局

```
Guest内存:
┌─────────────────────────────────────────┐
│             Vring共享区域                │
│  ┌─────────────────────────────────────┐│
│  │ desc表（描述符数组）                 ││
│  │ [0] addr|len|flags|next             ││
│  │ [1] addr|len|flags|next             ││
│  │ ...                                 ││
│  └─────────────────────────────────────┘│
│  ┌──────────┐    ┌──────────┐          │
│  │ avail环  │    │ used环   │          │
│  │ ring[]   │    │ ring[]   │          │
│  └──────────┘    └──────────┘          │
└─────────────────────────────────────────┘
         ↓ GPA→HVA映射
┌─────────────────────────────────────────┐
│             QEMU访问                     │
└─────────────────────────────────────────┘
```

## 参考

- [[summaries/virtio-vring-mechanism]]
- [[concepts/virtio]]
- [[concepts/ioeventfd-irqfd]]