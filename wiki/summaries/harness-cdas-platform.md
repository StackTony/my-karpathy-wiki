---
title: Harness CDaaS 平台体验
created: 2026-05-14
updated: 2026-05-14
tags: [CI/CD, Harness, CDaaS, DevOps]
credibility: low
source_dir: Self learn
source_files: [Harness-腾讯云-持续交付平台体验.md]
---

# Harness CDaaS 平台体验

> ⚠️ **低可信度警告**：本文来源于 AI 自主收集的网络博客，未经用户审核确认。请谨慎参考，建议阅读原文验证。

Harness CDaaS（Continuous Delivery as a Service）平台提供应用程序交付无缝自动化方法。

---

## 平台核心能力

| 功能 | 说明 |
|------|------|
| **自动检测新版本** | 监测GitHub、Jenkins、Artifactory、Nexus等仓库 |
| **图形化流水线** | YAML文件自动化构建管道，可视化设计 |
| **机器学习评估** | ML算法评估部署质量，自动回滚决策 |
| **监控工具集成** | AppDynamics、New Relic、Splunk、Elastic Search |

---

## 平台界面功能

### 应用管理

- 新建应用
- 选择监控工具集成
- 流水线状态追踪

### 流水线执行

- 制品根据构建ID获取
- 执行过程可视化
- 构建浏览器通知

### 用户交互

- 流水线中用户交互节点
- 度量数据展示（可自定义Dashboard）

---

## 关键特性

1. **无缝自动化**：新版本触发→DevOps团队警报→自动化部署
2. **质量评估**：ML算法分析监控数据判断部署健康
3. **自动回滚**：异常时从工具数据自动触发回滚

---

## 相关链接

- [[summaries/harness-cicd-overview]] - Harness开源CI/CD详解
- [[summaries/harness-pipeline-design]] - Pipeline设计指南
- [[summaries/kubernetes-core-concepts]] - K8s编排基础