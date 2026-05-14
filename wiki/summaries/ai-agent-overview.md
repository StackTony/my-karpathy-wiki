---
title: AI Agent 智能体概述
created: 2026-05-13
updated: 2026-05-13
tags: [AI, Agent, 智能体, Claude]
source_dir: AI人工智能/Agent架构
source_files: [Agent 智能体.md, Claude Code 安装.md]
---

# AI Agent 智能体概述

AI Agent（智能体）是能够自主决策、调用工具、与环境交互的 AI 系统。

---

## Agent 概念

| 特性 | 说明 |
|------|------|
| **自主决策** | 根据目标自主选择行动路径 |
| **工具调用** | 调用外部工具完成任务 |
| **环境交互** | 与外部系统交互获取信息 |
| **反馈循环** | ReAct 模式：Think → Act → Observe |

### Agent 学习资源

| 项目 | 链接 | 说明 |
|------|------|------|
| GenericAgent | https://github.com/lsdefine/GenericAgent | 复旦大学研究项目 |
| hello-agents | https://github.com/datawhalechina/hello-agents | 入门级智能体教程 |

---

## Claude Code 安装

### 安装方式

| 方式 | 说明 |
|------|------|
| 原生二进制 | 直接下载 claude.exe |
| npm 安装 | `npm install @anthropic-ai/claude-code` |

### npm 安装后路径

```
C:/Users/<用户>/AppData/Roaming/npm/node_modules/@anthropic-ai/claude-code/bin/claude.exe
```

---

## 相关链接

- [[summaries/langchain-architecture]] - LangChain Agent 架构详解
- [[concepts/ai-agent]] - Agent 智能体概念
- [[concepts/langchain]] - LangChain 框架