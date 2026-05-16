---
title: eBPF 内核可编程技术
created: 2026-05-16
updated: 2026-05-16
tags: [eBPF, Linux内核, 网络性能, Cilium]
---

# eBPF 内核可编程技术

eBPF（Extended Berkeley Packet Filter）是 Linux 内核的革命性可编程框架。

---

## 核心原理

| 特性 | 说明 |
|------|------|
| **安全注入** | 用户程序注入内核，验证器确保安全 |
| **JIT编译** | 编译为原生机器码，性能极高 |
| **钩子挂载** | 挂载到网络、跟踪、安全等钩子点 |
| **即时执行** | 数据包到达时直接触发eBPF程序 |

---

## vs 传统方式

| 方式 | 特点 | 性能 |
|------|------|------|
| **iptables** | 静态规则链，O(n)遍历 | 低 |
| **eBPF** | 哈希表查找，O(1)原生执行 | 极高 |

---

## Cilium：K8s网络终极形态

Cilium 全面拥抱 eBPF：

| 功能 | 实现 |
|------|------|
| **替换kube-proxy** | eBPF直接查Service→Pod映射 |
| **L7网络策略** | HTTP方法级别控制（GET允许，DELETE禁止） |
| **高效路由** | 无封装，直接转发 |

---

## 应用场景

| 场景 | 工具 |
|------|------|
| **网络加速** | Cilium、Katran |
| **可观测性** | bpftrace、Pixie |
| **安全监控** | Falco、Tracee |
| **性能分析** | perf eBPF扩展 |

---

## 相关链接

- [[summaries/k8s-technical-principles]] - K8s技术原理（eBPF章节）
- [[concepts/bpftrace]] - bpftrace动态追踪
- [[summaries/perf-tool]] - perf性能工具