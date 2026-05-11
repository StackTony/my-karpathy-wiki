---
title: Virtio Vring 数据共享机制
created: 2026-05-11
updated: 2026-05-11
tags: [virtio, vring, 共享内存, 数据传输]
sources: [Linux虚拟化/IO虚拟化/3）数据共享机制]
---

# Virtio Vring 数据共享机制

Vring 是 Virtio 数据面的核心，通过共享内存环形缓冲区实现前后端数据交换。

## Vring 结构

```c
struct vring {
    unsigned int num;
    struct vring_desc *desc;   // 描述符表
    struct vring_avail *avail; // 可用环
    struct vring_used *used;   // 已用环
};
```

## 三个核心表

### vring_desc（描述符表）
存储IO请求的GPA地址，通过next域组成链表：

```c
struct vring_desc {
    __virtio64 addr;   // GPA地址
    __virtio32 len;    // 长度
    __virtio16 flags;  // NEXT/WRITE标志
    __virtio16 next;   // 链表下一项
};
```

### vring_avail（可用环）
前端告诉后端有新请求可取：

```c
struct vring_avail {
    __virtio16 flags;
    __virtio16 idx;      // 下一个可用位置
    __virtio16 ring[];   // desc链表头位置
};
```

### vring_used（已用环）
后端告诉前端IO已完成：

```c
struct vring_used {
    __virtio16 flags;
    __virtio16 idx;
    struct vring_used_elem ring[];  // id + len
};
```

## 生产者-消费者模型

| 方向 | 生产者 | 消费者 |
|------|--------|--------|
| 前端→后端（请求） | 前端驱动 | 后端QEMU |
| 后端→前端（响应） | 后端QEMU | 前端驱动 |

## 操作流程

### virtqueue_add_buf（前端添加请求）
```
1. 找空闲desc表项 → 存IO请求GPA地址
2. 设置flags(NEXT/~NEXT) + next域 → 组成链表
3. 链表头位置写入avail->ring[idx]
4. idx++ → kick通知后端
```

### virtqueue_get_buf（后端获取请求）
```
1. 从avail取出数据（到idx位置）
2. 根据ring值找desc链表头 → 按next遍历
3. 封装IO请求 → 发送硬件
4. 链表头写入used->ring[idx].id
5. idx++ → 中断通知前端
```

## 数据流向图

```
         vring_desc
    ┌─────────────────┐
    │ [0] addr,len    │←── avail指向链表头
    │     next→[1]    │
    │ [1] addr,len    │
    │     next→[2]    │
    │ [2] addr,len    │←── used标记已处理
    │     next=NULL   │
    └─────────────────┘
         ↓ avail ring              ↓ used ring
    ┌──────────────┐         ┌──────────────┐
    │ ring[0]=0    │         │ ring[0].id=0 │
    │ ring[1]=3    │         │ ring[1].id=3 │
    └──────────────┘         └──────────────┘
```

## 关键点

- **共享内存**: Vring在Guest内存中，QEMU通过GPA→HVA映射访问
- **零拷贝**: 数据直接在共享内存中传递，无需额外拷贝
- **链表结构**: 一个IO请求可能占用多个desc项（scatter-gather）

## 相关链接

- [[summaries/virtio-architecture]]
- [[summaries/virtio-notification-mechanism]]
- [[summaries/virtio-device-types]]