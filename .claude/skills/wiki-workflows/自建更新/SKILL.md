# 自建更新

执行 Ingest 工作流，检查 raw/sources 变化并生成摘要和概念页面。

## 执行步骤

1. **检查变更**：使用 git status 或文件对比检查 `raw/sources` 目录下是否有新增或修改的 `.md` 文件

2. **处理变更**（如果有）：
   - 读取新增的源文件
   - 创建对应的 `wiki/summaries/[source-name].md` 摘要
   - 创建相关的 `wiki/concepts/[topic-name].md` 概念页面
   - 更新 `wiki/index.md` 索引（注意去重）
   - 更新 `wiki/log.md` 日志

3. **无变更时**：报告"本次检查无文档变更"

## 可信度处理

| 来源目录 | 处理方式 |
|----------|----------|
| `raw/sources/`（除 Self learn） | 正常创建摘要，高可信度 |
| `raw/sources/Self learn/` | 添加 `credibility: low` 字段 |

## Frontmatter 格式

```yaml
---
title: 页面标题
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [标签1, 标签2]
source_dir: 目录路径
source_files: [文件1.md, 文件2.md]
credibility: low  # 仅 Self learn 来源需要
---
```

## 相关链接

- [[CLAUDE.md]] - Wiki 规范和工作流程
- [[wiki/index.md]] - 内容目录