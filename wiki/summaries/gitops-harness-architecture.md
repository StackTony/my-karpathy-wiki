---
title: GitOps 架构详解（Harness + VKS）
created: 2026-05-14
updated: 2026-05-14
tags: [GitOps, CI/CD, Harness, Kubernetes]
credibility: low
source_dir: Self learn
source_files: [Harness-Broadcom-GitOps架构详解.md]
---

# GitOps 架构详解（Harness + VKS）

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

vSphere Supervisor 9.0 GitOps 架构，集成 Harness CI/CD 与 VKS（vSphere Kubernetes Service）集群。

---

## GitOps 定义

**GitOps 是运营框架**：
- 将 DevOps 最佳实践（版本控制、协作、合规、CI/CD）应用于基础设施自动化
- Git 仓库是唯一事实来源
- CI/CD 管道自动化部署和同步期望状态

---

## 架构核心原则

| 原则 | 说明 |
|------|------|
| **Git单一事实来源** | 所有基础设施和应用配置存储在Git |
| **自动化** | CI/CD管道自动化构建、测试、部署 |
| **可观测性** | 全面监控和追踪系统健康性能 |
| **安全** | 安全扫描和密钥管理集成 |

---

## 组件架构

| 组件 | 作用 |
|------|------|
| **GitLab** | Git仓库：源码、IaC、配置文件 |
| **Harness** | 主CI/CD平台：编排部署、管理管道、发布自动化 |
| **Artifactory** | 通用仓库管理：制品、Docker镜像 |
| **Dynatrace** | 端到端可观测：APM、基础设施监控 |
| **HashiCorp Vault** | 密钥管理：API key、密码、证书 |
| **Wiz** | 云安全：CSPM、CWPP、持续监控 |

---

## CI/CD 管道阶段

| 阶段 | 操作 |
|------|------|
| **1. Code Commit** | 开发者提交→GitLab webhook触发Harness CI→静态分析→构建→推送Artifactory |
| **2. Artifact Management** | 制品存储、版本控制、支持回滚 |
| **3. Security Scanning** | Wiz预部署扫描镜像和IaC→持续监控 |
| **4. Secrets Management** | Vault集中存储→动态密钥生成→Harness集成获取 |
| **5. CD Pipeline** | CI成功触发CD→GitOps reconciliation→部署到K8s→回滚策略 |
| **6. Observability** | Dynatrace自动注入→端到端监控→问题检测→反馈循环 |

---

## 安全集成架构

### Wiz 安全扫描

- **Pre-deployment**：扫描Docker镜像和IaC模板
- **Continuous Monitoring**：监控云环境和部署工作负载

### Vault 密钥管理

- 集中存储敏感信息
- 动态密钥生成（减少长期凭证风险）
- Harness集成：部署时获取密钥

---

## 回滚策略

Harness 提供强大回滚能力：
- 快速恢复到前一稳定状态
- 部署失败或问题时自动触发

---

## 相关链接

- [[summaries/harness-cicd-overview]] - Harness CI/CD详解
- [[summaries/kubernetes-core-concepts]] - K8s核心概念
- [[summaries/k8s-architecture-official]] - K8s官方架构
- [[concepts/kubernetes]] - K8s概念页