---
title: Linux 网络协议栈
created: 2026-05-11
updated: 2026-05-11
tags: [network, 网络, kernel, tcp, udp]
sources: [Linux 网络协议栈]
---

## 概述

Linux网络协议栈是内核处理网络数据包的完整系统，从Socket API到物理层封装。

## 核心架构

### 分层模型

| 层次 | 功能 | 关键结构 |
|------|------|----------|
| 应用层 | Socket API | struct socket, struct sock |
| 传输层 | TCP/UDP | tcp_sendmsg, udp_sendmsg |
| 网络层 | IP路由、分片 | ip_queue_xmit |
| 链路层 | Device抽象 | net_device |
| 物理层 | DMA传输 | rx_ring |

## 发送端流程

### 1. 应用层

```
socket() → sock_create() → struct socket + struct sock
connect() → TCP三次握手 → MSS协商
send() → sock_sendmsg → tcp_sendmsg/udp_sendmsg
```

### 2. 传输层（TCP）

```
tcp_sendmsg:
  1. 检查连接状态
  2. 创建 sk_buffer (skb)
  3. 构造 TCP header
  4. 计算 checksum + sequence number
  5. 调用 ip_queue_xmit
```

### 3. 网络层（IP）

```
ip_queue_xmit:
  1. 检查路由 (ip_route_output)
  2. 填充 IP header
  3. IP 分片（如需要）
  4. 设置链路层报头
```

### 4. 物理层

```
DMA拷贝 → 添加以太网header → CSMA/CD发送 → 中断通知完成
```

## 接收端流程

### 1. 物理层+链路层

```
网卡接收 → DMA到 rx_ring → 中断 → 分配 skb → 软中断 NET_RX_SOFTIRQ
```

### 2. 网络层

```
ip_rcv → checksum检查 → ip_defragment → ip_route_input
  → 本机: ip_local_deliver → tcp_v4_rcv/udp_rcv
  → 转发: dst_input → IP fragmentation → dev_queue_xmit
```

### 3. 传输层+应用层

```
tcp_v4_rcv → _tcp_v4_lookup → tcp_prequeue → socket receive queue
recv() → tcp_recvmsg → 拷贝到 user buffer
```

## 核心数据结构

### sk_buff

Socket kernel buffer，贯穿网络栈处理的核心结构。

```c
struct sk_buff {
    struct sk_buff *next, *prev;
    struct sock *sk;
    struct net_device *dev;
    
    union { struct tcphdr *th; struct udphdr *uh; } h;  // 传输层header
    union { struct iphdr *iph; } nh;                     // 网络层header
    
    unsigned int len, data_len;
    char cb[40];  // control block，各协议层私有信息
};
```

### Driver Queue

IP栈与NIC驱动之间的FIFO ring buffer，保存skb指针。

- 默认最大skb大小：MTU（通常1500 bytes）
- 大数据传输导致IP分片

## 关键概念

| 概念 | 说明 |
|------|------|
| MSS | Maximum Segment Size，TCP最大分段大小 |
| MTU | Maximum Transmission Unit，网卡最大传输单元 |
| NAPI | 新API，驱动通知内核的机制 |

## 相关链接

- [[concepts/linux-networking]]
- [[virtio-net内核态网络转发流程]]