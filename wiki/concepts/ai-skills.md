---
title: AI Skills 技能库
created: 2026-05-13
updated: 2026-05-13
tags: [AI, Skills, Agent]
source_dir: AI人工智能/Skills
source_files: [my-skills.md, 开源skills库.md]
---

# AI Skills 技能库

Skills 是 Agent 可调用的技能模块，扩展 Agent 的能力边界。

---

## Skills 概念

| 特性 | 说明 |
|------|------|
| **可复用** | 技能可在多个 Agent 间共享 |
| **可组合** | 多个 Skills 组合完成复杂任务 |
| **标准化** | 统一的输入输出接口 |

---

## Skills 资源

| 资源 | 链接 | 说明 |
|------|------|------|
| skills.sh | https://skills.sh/ | 开源 Skills 库平台 |
| my-skills | https://github.com/StackTony/my-skills | 个人 Skills 库 |

---

## 与 Agent 关系

```
Agent → 决策调用哪个 Skill → Skill 执行 → 返回结果 → Agent 继续推理
```

---

## 相关链接

- [[concepts/ai-agent]] - Agent 智能体
- [[summaries/langchain-architecture]] - LangChain Tool 章节