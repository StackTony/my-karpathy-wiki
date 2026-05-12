---
title: Linux 网络协议
created: 2026-05-12
updated: 2026-05-12
tags: [Linux, 网络, 协议]
---

# Linux 网络协议

Linux网络协议栈遵循TCP/IP分层模型。

## 分层架构

```
应用层 → Socket API
传输层 → TCP/UDP
网络层 → IP/ICMP/ARP
链路层 → Ethernet Driver
```

## 核心协议对比

| 协议 | 类型 | 可靠性 | 连接 | 适用场景 |
|------|------|--------|------|----------|
| TCP | 传输层 | 可靠 | 面向连接 | Web、文件传输 |
| UDP | 传输层 | 不可靠 | 无连接 | DNS、直播、游戏 |
| IP | 网络层 | - | - | 路由、分片 |
| ICMP | 网络层 | - | - | ping、诊断 |
| ARP | 链路层 | - | - | 地址解析 |

## 相关链接

- [[summaries/linux-network-stack]]
- [[summaries/linux-network-protocols]]