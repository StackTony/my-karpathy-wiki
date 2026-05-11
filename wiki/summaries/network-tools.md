---
title: 网络性能工具
created: 2026-05-11
updated: 2026-05-11
tags: [网络, iperf, 带宽, DFX]
sources: [DFX工具/==网络==]
---

# 网络性能工具

网络带宽测试工具 iperf 使用指南。

## iperf 基本用法

### 服务端（虚拟机 1）
```bash
iperf -s -p 20000 -u
# -s: server 模式
# -p: 端口号
# -u: UDP 模式
```

### 客户端（虚拟机 2）
```bash
iperf -c 195.168.1.31 -p 20000 -i 1 -l 1460 -t 18000 -u -b 50G
# -c: 连接目标 IP
# -p: 端口号
# -i: 报告间隔（秒）
# -l: 缓冲区长度（字节）
# -t: 持续时间（秒）
# -u: UDP 模式
# -b: 目标带宽
```

## TCP vs UDP 测试

| 模式 | 特点 | 适用场景 |
|------|------|---------|
| TCP | -t 参数可省略，默认一直运行 | 网络吞吐测试 |
| UDP | 需指定 -b 带宽上限 | 带宽极限测试 |

## 常见参数

| 参数 | 说明 |
|------|------|
| `-s` | 服务端模式 |
| `-c <IP>` | 客户端模式 |
| `-p <port>` | 端口号 |
| `-t <seconds>` | 测试时长 |
| `-i <seconds>` | 报告间隔 |
| `-l <bytes>` | 缓冲区大小 |
| `-u` | UDP 模式 |
| `-b <bandwidth>` | 目标带宽 |
| `-P <num>` | 并行连接数 |

## 相关链接

- [[summaries/linux-network-stack]]
- [[summaries/dfx-tools-overview]]