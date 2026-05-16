---
title: Harness CD Pipeline 设计指南（官方）
created: 2026-05-14
updated: 2026-05-16
tags: [CI/CD, Harness, Pipeline, DevOps]
source_type: wiki-derived
---

# Harness CD Pipeline 设计指南（官方）

> **来源说明**：本文整理自 Harness 官方文档最佳实践，原始源文件已缺失。

Harness 官方 Pipeline 设计最佳实践，覆盖版本控制、自动化、环境一致性、反馈循环。

---

## 版本控制

### 使用版本控制系统

- 将 Pipeline 存储在 Git 提供商
- Git 提供追踪、审批、管理变更机制
- 从 Harness UI 限制变更，确保 Git 为事实来源

### 采用分支策略

| 策略 | 说明 |
|------|------|
| Feature Branching | 功能分支开发 |
| Git Flow | 主干+开发+功能+发布分支 |
| Release Branching | 发布分支管理 |
| Environment Branching | 按环境分支 |
| Trunk Based Development | 主干开发 |

---

## 自动化一切

| 原则 | 说明 |
|------|------|
| **管道自动化测试、部署、监控** | 确保高质量制品部署 |
| **使用 Deploy Stage** | 定义和自动化部署流程 |
| **集成 APM 验证** | Verify Step 连接APM，AI/ML辅助自动回滚决策 |
| **使用内置测试 Step** | Shell Script/HTTP/Plugin/Run Step |
| **避免手动干预** | 自动化验证减少人工，一致性决策 |

---

## Build Once and Deploy

**一次构建，全阶段使用同一制品**：
- 低环境验证的制品传播到高环境
- Harness **propagate service** 功能跨环境传播服务和制品

---

## 环境一致性

- Staging、Production、Development 环境尽可能相似
- Harness service 和 environment 配置管理确保变量一致
- Templates 定义服务无关 Stage，部署任意环境

---

## Fast Feedback Loops

### Fail Fast

- Harness 管道默认快速失败
- Step 执行失败→Stage 失败→Initiate rollback

### 即时反馈

- 通知开发者管道成功/失败
- 集成 MS Teams、Slack、Email

### 回滚能力

- Harness 自动添加 rollback step 到部署 Stage
- 默认失败时回滚
- 用户可定制 rollback 行为

---

## Infrastructure-as-Code (IaC)

| 工具 | 说明 |
|------|------|
| Terraform | 声明式基础设施定义 |
| Terragrunt | Terraform wrapper |
| CloudFormation | AWS IaC |
| Azure ARM/Blueprint | Azure IaC |

**Harness IaC 集成**：
- 部署失败时回滚应用和基础设施
- 动态预配：部署时预配所需基础设施

---

## 并行和顺序 Stage

- Harness 支持顺序、并行、混合 Stage
- **Matrix Deployment**：多服务到多环境，一个 Stage
- Reduce Stage 数量，确保部署一致性

---

## Manual Approval Steps

敏感部署或入门阶段：
- 开发者和发布经理验证部署
- 随着信心增加，逐步减少审批

---

## Self-Service Deployments

- 给开发团队自己的 Project
- 团队管理自己的发布管道、服务、集成
- Pipeline Triggers 自动化部署
- Input Sets 提供受控输入

---

## 监控、日志、告警

| 集成 | 说明 |
|------|------|
| **Prometheus/ELK** | 监控和日志工具 |
| **Continuous Verification** | APM 指标衡量性能，自动判断回滚 |
| **Pipeline Notifications** | 通知用户管道事件 |
| **Failure Strategies** | 定义失败时动作 |

---

## Continuous Improvement

**定期审查和更新 CD 管道**：

- 如何改进 QA 签核时间？
- 如何改进部署发布时间？
- 还有哪些流程可自动化？
- 如何减少管道维护数量？
- 如何强制所有服务部署相同步骤序列？

**监控指标**：
- Lead time
- Failure rate
- Harness 产品仪表盘和自定义仪表盘

---

## 相关链接

- [[summaries/harness-cicd-overview]] - Harness CI/CD详解
- [[summaries/gitops-harness-architecture]] - GitOps架构
- [[summaries/kubernetes-core-concepts]] - K8s核心概念