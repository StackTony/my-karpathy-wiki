# LLM Wiki 规范

这是一个 Karpathy 风格的 LLM Wiki。LLM 是程序员；你是产品经理和审核者。

## 架构

三层结构：
1. **Raw sources** - `raw/sources/` 中的原始文档
2. **Wiki** - `wiki/` 中 LLM 生成的 markdown 文件
3. **Schema** - 本文件，定义约定和工作流程

## 目录结构

```
karpathy-wiki/
├── CLAUDE.md         # 本文件 - 规范和工作流程
├── README.md         # 项目概述
├── raw/
│   ├── sources/      # 原始文档（PDF、文章、笔记）
│   └── resources/    # 下载的图片和媒体资源
└── wiki/
    ├── entities/     # 实体页面（人物、公司、产品）
    ├── concepts/     # 概念页面和主题摘要
    ├── summaries/    # 来源文档摘要
    ├── recommendations/ # 学习推荐报告（每日生成）
    ├── index.md      # 内容目录（自动更新）
    └── log.md        # 按时间顺序的操作日志
```

## 核心约定

### 页面命名
- 使用 kebab-case：`machine-learning-foundations.md`
- 实体：`[[entities/person-name]]`
- 概念：`[[concepts/topic-name]]`

### Frontmatter
```yaml
---
title: 页面标题
created: 2026-05-02
updated: 2026-05-02
tags: [标签1, 标签2]
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

### Cross-References（交叉引用）
- 使用 wiki-links：`[[entities/entity-name]]`
- 始终链接到相关概念

## 工作流程

### Ingest（添加新来源）
1. 从 `raw/sources/` 读取来源文档
2. 将摘要写入 `wiki/summaries/[source-name].md`
3. 更新 `wiki/index.md` 添加新条目
4. 更新/创建 `wiki/entities/` 中的相关实体页面
5. 更新/创建 `wiki/concepts/` 中的概念页面
6. 在 `wiki/log.md` 中追加条目

### Query（回答问题）
1. 读取 `wiki/index.md` 查找相关页面
2. 阅读相关页面
3. 综合回答并附上引用
4. 输出的形式可以很多：Markdown、对比表格、幻灯片、图表
5. **重要**：如果回答有价值，将其存档为新的 wiki 页面

### Lint（健康检查）
定期结合原始文件检查wiki，发现并处理以下动作：
- 找出矛盾的数据或页面之间的矛盾
- 标记过时的结论或被新来源取代的过时陈述
- 清理孤立页面或没有入站链接的孤立页面
- 发现数据缺口或缺失的交叉引用
- 自动搜索补全空白或被提及但缺少页面的概念

### Learn（学习推荐新知识）
每日自动执行的学习推荐流程，发现并整理新知识：

**前置条件**：用户在 `wiki/recommendations/1-Day learn.md` 的"优先了解"章节指定学习主题

**执行步骤**：
1. **检查学习主题**：读取 `wiki/recommendations/1-Day learn.md`，检查"### 优先了解："章节
   - **如果为空**：直接报告"今日无学习主题，任务结束"，退出流程
   - **如果有内容**：继续执行后续步骤
2. **分析已有知识**：搜索 wiki 目录，识别知识库中已有的相关内容
3. **联网搜索**：使用 tavily-search 搜索技术博客（偏好：原理介绍、技术分析、架构图）
4. **生成推荐报告**：
   - 已有知识总结（附 wiki-links）
   - 推荐博客清单（标题 + 链接 + 类别 + 摘要 + 重点内容）
   - 学习路径建议（入门→进阶）
   - 知识图谱关联建议
5. **保存报告**：写入 `wiki/recommendations/YYYY-MM-DD.md`
6. **下载博客**：使用 Defuddle CLI 将推荐的所有博客保存到 `raw/sources/Self learn/`（标记为低可信度）

**注意**：只检查"优先了解"章节，"后续了解"部分不作为当日推荐依据

**报告格式**：
```yaml
---
title: 学习推荐报告 - YYYY-MM-DD
created: YYYY-MM-DD
tags: [学习推荐, 每日报告]
---

# 学习推荐报告

## 一、用户指定主题
来自[[recommendations/1-Day learn]]：学习方向 + 博客类别

## 二、已有知识总结
分析 wiki 中相关内容，标记 ✅ 已有 / ❌ 缺失

## 三、深入推荐
按主题分组的博客推荐表格

## 四、学习路径建议
入门→进阶的学习路线图

## 五、知识图谱关联建议
与现有 wiki 页面的关联
```

**搜索来源**：不限于 CSDN/知乎，扩展到掘金、博客园、GitHub、官方文档等


## 索引格式

`wiki/index.md` 应包含：
- 分类标题（## Summaries, ## Entities, ## Concepts）
- 每个条目：`[页面名称](链接)` - 一行摘要 - 元数据

## 日志格式

`wiki/log.md` 使用前缀：`## [YYYY-MM-DD] 操作 | 标题`

示例：
```markdown
## [2026-05-02] ingest | 文章：神经网络入门
- 创建 summaries/neural-networks-intro.md
- 更新 concepts/deep-learning.md
- 从 entities/backpropagation.md 添加链接
```

## 最根本原则

- **Wiki 是持久的** - 知识编译一次，保持最新并可以持续增量构建积累。即知识是一次性编译好的，不是每次查询重新推导。
- **LLM 写，人读** - LLM 负责所有的整理维护工作。其中原始资料（Raw Sources）内容不可变，AI 只读不写；而Wiki（知识库）才是 AI 完全拥有和维护的领地。
- **交叉引用是最重要的部分** - 链接与内容同等重要
- **有价值的回答变成页面** - 积累你的知识，分析整合到已有的知识库
- **渐进式披露** - 先传递核心信息、再根据需求逐步补充细节，知识随时间深化，不是一次性暴露
- **自我反思** - 每次回复前，自动执行两条"神级"指令：
  1. **反驳自己**：主动审视自己的理解、方案是否有漏洞或错误
  2. **检查遗漏**：主动思考是否遗漏了重要信息、规则或细节

## 补充规则
#### 全局联网搜索规则
1. 禁止使用 Claude Code 原生 WebSearch、WebFetch 工具
2. 所有网络搜索、查文档、查资料、查报错 必须使用 tavily-search MCP 插件
3. 优先用 tavily-search 做全网检索，再解析页面内容
4. 当 tavily 每个月的使用次数到达限制无法使用后，可以重新启用 Claude Code 原生 WebSearch、WebFetch 等工具尝试搜索

#### 可信度分级规则
1. 对原始文档进行来源可信度分级索引，索引建立时应对低可信度内容单独标记。注意可信度分级规则只针对于**原始文档**（raw目录下的）其他例如wiki目录下的不需要可信度分级。建议在 frontmatter 中添加 `credibility: low` 字段，或在 index.md 中使用独立分类，高低可信度的划分依据为：
    - **高可信度**：`raw/sources/` 下除 `Self learn` 外的所有目录，内容经过用户审核确认
    - **中可信度**：内容为提问过程中AI基于笔记分析整理和自行搜索汇总出来的放在raw下的文档
    - **低可信度**：`raw/sources/Self learn/` 目录，内容为 AI 自主探索收集的网络博客，未经用户审核确认
2. 当新增的内容与本地记录的已有内容有冲突时，可以主动寻求用户审核确认来提升新增内容的可信度

#### 用户使用AI时的规则
给AI交流时（比如使用Obsidian里的opencode或claudian插件时），除特别通用的常识（今天几号）、规则类问题（执行xxx命令）回复外，其他复杂任务的每次回复前，自动执行两条"神级"指令：
- 第一条指令是：**“反驳我”。**
- 第二条指令是：**“我遗漏了什么？”。**