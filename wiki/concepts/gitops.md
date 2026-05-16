---
title: GitOps 声明式运维范式
created: 2026-05-16
updated: 2026-05-16
tags: [GitOps, DevOps, Kubernetes, ArgoCD]
---

# GitOps 声明式运维范式

GitOps 是 Kubernetes 时代的终极运维哲学，Git 仓库为唯一事实来源。

---

## 核心思想

| 原则 | 说明 |
|------|------|
| **声明式** | YAML描述期望状态 |
| **版本化** | Git历史不可篡改 |
| **自动应用** | 工具自动同步 |
| **持续调谐** | 检测漂移并修正 |

---

## Pull vs Push 模式

| 模式 | 工具 | 流程 |
|------|------|------|
| **Push** | Jenkins | CI → kubectl apply → K8s |
| **Pull** | ArgoCD | Git → ArgoCD监听 → 自动拉取 |

---

## ArgoCD 工作原理

1. **监听 Git 仓库**：持续对比期望状态与实际状态
2. **检测漂移**：发现不一致立即报警
3. **自动修正**：将集群状态同步回Git声明
4. **一键回滚**：`git revert` → 自动恢复

---

## 优势

| 优势 | 说明 |
|------|------|
| **杜绝漂移** | 手动修改会被自动修正 |
| **完美审计** | Git log记录谁改了什么 |
| **一键回滚** | 版本回退无需手动操作 |
| **安全合规** | Git作为唯一入口 |

---

## 相关链接

- [[summaries/k8s-technical-principles]] - K8s技术原理（GitOps章节）
- [[concepts/kubernetes]] - Kubernetes概念