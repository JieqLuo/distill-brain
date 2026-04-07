# distill-brain

面向 Claude Code 的 AI-Native 个人知识管理。

把对话变成结构化、可导航的知识库。纯 markdown 文件，零后台进程，为 AI agent 消费信息的方式而设计。

[English README](README.md)

## 它做什么

`distill-brain` 从你的 Claude Code 对话中提取可迁移的知识——决策、模式、洞察、问题排查——组织成分层的 markdown 知识库，agent 可以高效导航。

**不是又一个 RAG 系统。** 没有向量数据库，没有 embedding，没有后台进程。只有 markdown 文件 + 三层索引，agent 按需读取。

## 工作原理

```
你的对话
       ↓  /distill（你确认哪些值得保存）
~/.claude/kb/
├── INDEX.md              ← agent 先读这个（~50行）
├── domains/
│   ├── ai-engineering/
│   │   ├── INDEX.md      ← 再缩小到某个领域（~30行）
│   │   └── patterns.md   ← 最后读具体条目
│   └── tools/
├── inbox/
└── journal/
```

**三次读取找到任何知识。** 而不是把整个知识库塞进上下文。

## 安装

```bash
git clone https://github.com/JieqLuo/distill-brain.git ~/.claude/skills/distill-brain
```

完事。不需要依赖，不需要构建，不需要配置。

## 使用

### 从对话中提取知识

```
/distill
```

分析当前对话，识别可迁移的知识，展示候选列表等你确认。未经你明确同意不会写入任何东西。

### 重建索引

```
/distill-compile
```

从所有条目重新生成 INDEX.md 文件。手动编辑或导入后运行。

### 搜索知识库

```
/distill-search {查询词}
```

Agent 通过三层索引导航找到相关知识。最多 5 次文件读取。

### 可视化知识图谱

```
/distill-graph
```

扫描所有条目，构建关系图谱，识别 Hub 节点和知识聚类。输出 markdown 报告（`journal/graph-{date}.md`）和可交互 HTML 可视化（`graph.html`），浏览器双击即可打开。

### 知识质量审计

```
/distill-lint
```

检查过期条目、孤立条目、断链、重复内容。展示发现和修复建议，经你确认后执行。

### 分类收件箱

```
/distill-triage
```

处理 `inbox/` 中的条目——建议归属领域、推断关系、经你确认后移入知识库结构。

### 从 Claude Code Memory 导入

```
/distill-import
```

读取项目的 `MEMORY.md` 和 memory 文件，筛选出可迁移的跨项目知识导入知识库。项目专属的操作规则留在 auto memory 中。

## 知识存储位置

默认存储在 `~/.claude/kb/`，纯 markdown 文件。可以在 `.kb-config.yaml` 中修改路径。

**兼容 Obsidian。** 把 KB 路径指向 Obsidian vault 内的文件夹，知识条目就能在 Obsidian 中浏览、搜索、通过 `[[双链]]` 关联。

## 设计理念

**哲学优先于规则。** SKILL.md 不告诉 agent *提取什么*——而是给一个思考框架，让它自主*判断*什么值得提取。

**该硬的地方硬。** 条目格式、质量底线、安全规则不可违反。其余一切——分类、置信度、关系推断——由 agent 自主决定。

**零基础设施。** 没有数据库，没有服务器，没有后台进程。电脑崩了，你的知识还在，是人类可读的 markdown 文件。

**Agent 原生导航。** 三层渐进式加载意味着 agent 读 ~200 行就能找到所需知识，而不是加载你整个知识库。

## 知识条目格式

```yaml
---
title: 三层渐进式知识导航
domain: ai-engineering
created: 2026-04-07
source: session:distill-brain:2026-04-07
tags: [information-architecture, token-efficiency]
---

# 三层渐进式知识导航

> **Core insight:** 总索引 → 领域索引 → 条目，三次读取找到任何知识，共 ~200 行，无需加载整个知识库。

（条目正文——结构由 agent 根据内容类型自主决定）
```

## 对比

| 工具 | 方式 | 代价 |
|------|------|------|
| Claude Code Memory | 扁平 MEMORY.md | 无关系，不可扩展 |
| Obsidian + 手动 | 人工整理 | 对话知识容易遗漏 |
| 向量 DB 插件 | Embedding + 后台 agent | 高内存占用，长时间运行不稳定 |
| RAG 系统 | Embedding + 检索 | 基础设施开销大，不透明 |
| **distill-brain** | **分层 markdown 索引** | **纯文件，agent 可导航** |

## 路线图

- [x] Phase 1：核心循环（`/distill`、`/distill-compile`、`/distill-search`）
- [x] Phase 2：图谱 + 质量 + 分类（`/distill-graph`、`/distill-lint`、`/distill-triage`）
- [ ] Phase 3：Obsidian 同步、自动提取、跨项目知识共享

## 许可

MIT
