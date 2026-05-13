---
title: tcpdump 网络分析工具
created: 2026-05-13
updated: 2026-05-13
tags: [tcpdump, 网络, 抓包, DFX]
source_dir: DFX工具/==网络==
source_files: [网络分析工具tcpdump.md]
---

# tcpdump 网络分析工具

tcpdump 是 Linux 系统的网络数据包收集与分析工具，根据用户自定义条件截取数据包，是系统管理员分析网络、排查问题的利器。

---

## 一、基本介绍

| 特性 | 说明 |
|------|------|
| 权限要求 | 需要 root 权限执行 |
| 过滤能力 | 支持网络层、协议、主机、端口过滤 |
| 逻辑运算 | 支持 and、or、not 组合条件 |
| 输出格式 | 可输出到文件（.pcap），配合 Wireshark 分析 |

---

## 二、常用参数

| 参数 | 说明 |
|------|------|
| `-i <接口>` | 指定网络接口 |
| `-w <文件>` | 输出到文件（.pcap后缀） |
| `-n` | 不进行域名解析（速度更快） |
| `-nn` | 不进行端口名称转换 |
| `-v` / `-vv` | 输出详细/更详细的信息 |
| `-c <数量>` | 收集指定数量后停止 |
| `-C <大小>` | 指定写入文件大小上限（MB） |
| `-r <文件>` | 从文件读取数据包 |
| `-e` | 打印数据链路层头部信息 |
| `-t` | 不打印时间戳 |
| `-A` | ASCII格式打印分组内容 |

---

## 三、表达式语法

**格式**：`tcpdump [option] 协议 + 传输方向 + 类型 + 具体值`

### 组成要素

| 要素 | 可选值 | 默认值 |
|------|--------|--------|
| **协议** | ip、arp、rarp、tcp、udp、icmp、http | 所有协议 |
| **方向** | src、dst、dst or src、dst and src | src or dst |
| **类型** | host、net、port、ip proto | host |
| **逻辑** | not/!、and/&&、or/\|\| | - |

---

## 四、常用实例

### 主机过滤

```bash
# 包含主机192.0.0.19的数据包
tcpdump host 192.0.0.19

# 源IP是192.0.0.19
tcpdump src host 192.0.0.19

# 目标IP是192.0.0.19
tcpdump dst host 192.0.0.19
```

### 网段过滤

```bash
# 包含192.0.0.0/24网段的数据包
tcpdump net 192.0.0.0/24
```

### 端口过滤

```bash
# 9092端口的数据包
tcpdump port 9092

# 源端口是9092
tcpdump src port 9092

# 目标端口是9092
tcpdump dst port 9092
```

### 协议过滤

```bash
# TCP协议数据包
tcpdump tcp

# UDP协议数据包
tcpdump udp

# ICMP协议数据包
tcpdump icmp

# SSH服务数据包
tcpdump port 22
```

### 组合条件

```bash
# 源IP是192.0.0.19且目标端口是9092
tcpdump src host 192.0.0.19 and dst port 9092

# 源IP是192.0.0.19或目标端口是9092
tcpdump src host 192.0.0.19 or dst port 9092

# 源IP是192.0.0.19且端口是9092，或源IP是192.0.0.20且目的端口不是80
tcpdump '(src host 192.0.0.19 and port 9092) or (src host 192.0.0.20 and not dst port 80)'
```

### 文件操作

```bash
# 流经网卡eth0的1000个数据包保存到文件
tcpdump -i eth0 -c 1000 -w backup.cap

# 从文件读取tcp协议的10个数据包
tcpdump -r backup.cap -c 10 tcp
```

### 包大小过滤

```bash
# 包长度大于50，小于100的数据包
tcpdump 'greater 50 and less 100'
```

### 广播/多播

```bash
# 以太网广播数据包
tcpdump ether broadcast

# IPv4广播数据包
tcpdump ip broadcast

# 多播数据包
tcpdump ip multicast
```

---

## 五、高级用法

### 指定协议类型

```bash
# OSPF协议数据包
tcpdump ip proto ospf

# IP信息类型（icmp、icmp6、igmp、igrp、pim、ah、esp、vrrp、udp、tcp）
tcpdump ip proto icmp

# IPv6数据包
tcpdump ip6

# Ethernet层协议（ip、ip6、arp、rarp等）
tcpdump ether proto ip
```

### 结合 Wireshark

```bash
# 抓包保存
tcpdump -i eth0 -w capture.pcap -c 1000

# 用Wireshark打开分析
wireshark capture.pcap
```

---

## 六、与系统知识关联

| 系统模块 | 关联点 |
|----------|--------|
| [[summaries/linux-network-stack]] | 抓包位于网络协议栈各层 |
| [[summaries/network-tools]] | iperf打流配合tcpdump分析 |
| [[summaries/linux-network-protocols]] | TCP/UDP/ICMP协议过滤 |

---

## 相关链接

- [[summaries/network-tools]] - iperf带宽测试
- [[summaries/linux-network-stack]] - 网络协议栈
- [[summaries/dfx-tools-overview]] - DFX工具总览