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
sources: [source-id]
---
```

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