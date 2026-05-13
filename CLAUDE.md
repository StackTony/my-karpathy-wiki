# LLM Wiki Schema

This is a Karpathy-style LLM Wiki. The LLM is the programmer; you are the product manager and reviewer.

## Architecture

Three layers:
1. **Raw sources** - Immutable source documents in `raw/sources/`
2. **Wiki** - LLM-generated markdown files in `wiki/`
3. **Schema** - This file, defining conventions and workflows

## Directory Structure

```
karpathy-wiki/
├── CLAUDE.md         # This file - schema and workflows
├── README.md         # Project overview
├── raw/
│   ├── sources/     # Original documents (PDF, articles, notes)
│   └── resources/     # Downloaded images and media
└── wiki/
    ├── entities/   # Entity pages (people, companies, products)
    ├── concepts/   # Concept pages and topic summaries
    ├── summaries/  # Source document summaries
    ├── index.md    # Content catalog (auto-updated)
    └── log.md      # Chronological operation log
```

## Core Conventions

### Page Naming
- Use kebab-case: `machine-learning-foundations.md`
- Entities: `[[entities/person-name]]`
- Concepts: `[[concepts/topic-name]]`

### Frontmatter
```yaml
---
title: Page Title
created: 2026-05-02
updated: 2026-05-02
tags: [tag1, tag2]
source_dir: 目录路径
source_files: [文件1.md, 文件2.md]
---
```

### Sources 格式规范

采用 **目录 + 文件分离** 格式，精确追溯知识来源：

| 字段 | 说明 | 格式要求 |
|------|------|----------|
| `source_dir` | 来源目录（主题领域） | 不带 `raw/sources/` 前缀，不带文件名 |
| `source_files` | 具体文件列表 | 带 `.md` 后缀，相对于 `raw/sources/<source_dir>/` |

**示例**：
```yaml
# 单文件来源
source_dir: 数据结构与算法/树
source_files: [红黑树详解.md]

# 多文件整合
source_dir: Linux操作系统/Linux锁机制
source_files: [Linux 锁机制全景介绍.md, Linux SpinLock锁.md, Linux Mutex锁.md, Linux RCU锁.md]

# 跨目录整合（特殊情况）
source_dir: DFX工具
source_files: [==CPU==/perf工具分析虚拟机的性能事件.md, ==设置trace点==/perf工具.md]
```

**设计意义**：
- `source_dir`：主题领域一目了然，便于分类索引
- `source_files`：精确追溯每个来源文件，便于更新维护

### Cross-References
- Use wiki-links: `[[entities/entity-name]]`
- Always link to related concepts

## Workflows

### Ingest (Adding New Source)
1. Read the source document from `raw/sources/`
2. Write summary to `wiki/summaries/[source-name].md`
3. Update `wiki/index.md` with new entry
4. Update/create relevant entity pages in `wiki/entities/`
5. Update/create concept pages in `wiki/concepts/`
6. Append entry to `wiki/log.md`

### Query (Answering Questions)
1. Read `wiki/index.md` to find relevant pages
2. Read relevant pages
3. Synthesize answer with citations
4. **IMPORTANT**: If answer is valuable, file it back as new wiki page

### Lint (Health Check)
Periodically check for:
- Contradictions between pages
- Stale claims superseded by new sources
- Orphan pages with no inbound links
- Missing cross-references
- Concepts mentioned but lacking pages

## Index Format

`wiki/index.md` should contain:
- Category headers (## Summaries, ## Entities, ## Concepts)
- Each entry: `[Page Name](link)` - one-line summary - metadata

## Log Format

`wiki/log.md` uses prefix: `## [YYYY-MM-DD] operation | Title`

Example:
```markdown
## [2026-05-02] ingest | Article: Neural Networks Intro
- Created summaries/neural-networks-intro.md
- Updated concepts/deep-learning.md
- Added link from entities/backpropagation.md
```

## Principles

- **Wiki is persistent** - knowledge compiles once, stays current
- **LLM writes, human reads** - LLM does all maintenance work
- **Cross-references are first-class** - links are as valuable as content
- **Valuable answers become pages** - compound your knowledge
- **Progressive Disclosure** - knowledge deepens over time, not一次性exposure