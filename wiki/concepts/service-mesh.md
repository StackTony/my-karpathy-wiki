---
title: Service Mesh 服务网格
created: 2026-05-16
updated: 2026-05-16
tags: [ServiceMesh, Istio, Envoy, 微服务, mTLS]
---

# Service Mesh 服务网格

Service Mesh 解决集群内部（东西向）流量安全与治理问题。

---

## 核心概念

| 概念 | 说明 |
|------|------|
| **Sidecar注入** | 每个Pod自动注入Envoy代理 |
| **流量劫持** | iptables重定向所有进出流量 |
| **控制平面** | Istiod管理证书、策略 |
| **数据平面** | Envoy执行流量转发 |

---

## 工作原理

```
Pod内部:
┌─────────────────────────────┐
│  应用容器 (python-app)      │
│         ↓ ↑                 │
│  iptables重定向             │
│         ↓ ↑                 │
│  Envoy Sidecar              │ ← 共享NET Namespace
└─────────────────────────────┘
```

---

## Istio 能力

| 能力 | 说明 |
|------|------|
| **mTLS** | 自动双向TLS加密，零信任网络 |
| **精细化授权** | L7级别：仅允许HTTP GET /api/v1 |
| **韧性控制** | 超时、熔断、重试自动处理 |
| **流量管理** | 灰度发布、流量分割 |

---

## 与Ingress对比

| 维度 | Ingress | Service Mesh |
|------|---------|--------------|
| **流量方向** | 南北向（进出集群） | 东西向（集群内部） |
| **安全范围** | 边界入口 | 所有Pod间通信 |
| **透明性** | 需配置 | 对开发者完全透明 |

---

## 相关链接

- [[summaries/k8s-technical-principles]] - K8s技术原理（Service Mesh章节）
- [[concepts/kubernetes]] - Kubernetes概念