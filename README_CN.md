# distill-brain

你的 AI 第二大脑。从对话、代码库和实践中蒸馏你独特的理解——不是原始知识——沉淀成一个持续生长、可导航的知识体系。

基于 [Andrej Karpathy 的 LLM-wiki 三层架构](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。纯 markdown 文件，零后台进程，为 AI agent 消费信息的方式而设计。

[English README](README.md)

## 它做什么

Karpathy 的 LLM-wiki 证明了 LLM 可以维护个人知识库——自动摄入材料、交叉引用、保持一致性。**distill-brain 继承了这套架构，然后追问一个更难的问题：什么知识真正属于你？**

LLM 已经知道教科书内容。一个只是总结文章的 wiki 不过是在重新包装模型已有的东西。真正有价值的是模型*不知道*的——你的判断、你的实践验证、书上说的和你实际做的之间的差距。

**distill-brain = Karpathy 的三层架构 + 你处理过的理解。** 它捕获一切（自动回写到 inbox），但进入你正式知识库的只有你验证过、挑战过、通过自己思考确认过的东西。

没有向量数据库，没有 embedding，没有后台进程。只有 markdown 文件 + 三层架构，agent 按需读取。

### 有什么不同

- **存处理，不存知识。** LLM 已经知道教科书内容。你的 brain 只存它不知道的——你的判断、你的实践验证、理论和现实之间的差距。
- **三层架构。** 原始材料（不可变证据）→ Wiki（你的处理后知识）→ Schema（brain 的运行规则）。基于 Karpathy 的 LLM-wiki 设计，自动回写到 inbox 实现零摩擦知识捕获。
- **苏格拉底式学习。** `/distill-learn` 不只是保存你说的话，而是挑战你的理解、用 brain 已有知识测试跨领域迁移、只有证明真正掌握后才存入。
- **三层挑战。** `/distill-challenge` 用你的 brain 作为瞄准方向，但从外部拿弹药——找内部矛盾（第 1 层）、攻击盲区（第 2 层）、引入全新视角（第 3 层）。
- **Repo 理解。** `/distill-repo` 通过引导式问答帮你真正理解一个代码库——然后"编译"知识，之后每次访问节省 60-80% token。
- **认知模式。** 随着时间推移，agent 观察你怎么思考——不只是你知道什么——并存储提炼后的模式，帮助未来的 agent 更好地与你合作。
- **跨平台 brain。** 纯 markdown 文件。兼容 Obsidian、任何文本编辑器、或任何能读文件的 AI 工具。你的 brain 属于你，不绑定任何平台。

## 架构

```
                    Karpathy 三层架构
                    ────────────────

~/.claude/brain/
├── sources/              原始材料 — 不可变
│   ├── articles/         （LLM 只读，永远不改）
│   └── repos/
│
├── domains/              Wiki — 你的处理后知识
│   ├── ai-engineering/   （LLM 维护，需用户确认）
│   ├── learning/         
│   └── ...               
│
├── inbox/                自动回写缓冲区
│                         （LLM 自动捕获 → 用户 triage → domains/）
│
├── journal/              提取日志
└── .brain-config.yaml    Schema — 结构和约定
                          （配合 SKILL.md）
```

**三次读取找到任何知识。** 总索引 → 领域索引 → 条目，共 ~200 行。

**自动回写。** 有价值的查询结果自动捕获到 `inbox/`。用户运行 `/distill-triage` 审核：直接用 LLM 草稿（快）或回到源材料重新处理（深）。Karpathy 的速度 + distill-brain 的质量。

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

处理 `inbox/` 中的条目——包括手动提交和自动捕获的草稿。建议归属领域、推断关系、经你确认后移入知识库结构。

### 从 Claude Code Memory 导入

```
/distill-import           # 当前项目的 memory
/distill-import all       # 扫描所有项目
/distill-import {路径}    # 指定 memory 目录
```

读取 `MEMORY.md` 和 memory 文件，筛选出可迁移的跨项目知识导入知识库。项目专属的操作规则留在 auto memory 中。

### 验证式学习

```
/distill-learn {主题}
```

苏格拉底学习模式。通过提问验证你的理解——追问为什么和怎么做、用 brain 已有知识测试迁移能力、找矛盾和挑战薄弱环节。只有证明真正理解后才提取进 brain。条目标记 `confidence_source: verified`。

### 压力测试想法

```
/distill-challenge {想法或主题}
```

三层挑战模式。第 1 层：从你已有条目中找内部矛盾。第 2 层：攻击盲区（brain 里缺什么）。第 3 层：引入你知识图谱之外的全新视角。不迎合——挑战者必须表态，并说明什么证据能改变立场。经受住考验的想法标记 `confidence_source: challenged`。

### 理解代码库

```
/distill-repo {URL 或本地路径}
```

渐进式 repo 分析：文件树 + README → 架构文档 → 关键文件。通过苏格拉底式问答引导你理解设计决策。同时存储架构参考（总是存，因为代码可能不在训练数据里）和你验证过的洞察。之后每次访问节省 60-80% token。

## 知识存储位置

默认存储在 `~/.claude/brain/`，纯 markdown 文件。可以在 `.brain-config.yaml` 中修改路径。

**兼容 Obsidian。** 把 brain 路径指向 Obsidian vault 内的文件夹，知识条目就能在 Obsidian 中浏览、搜索、通过 `[[双链]]` 关联。条目维护双视图关系：`related` YAML 给 agent 用 + `## Related` wikilinks 给人看。

## 设计理念

**哲学优先于规则。** SKILL.md 不告诉 agent *提取什么*——而是给一个思考框架，让它自主*判断*什么值得提取。

**该硬的地方硬。** 条目格式、质量底线、安全规则不可违反。其余一切——分类、置信度、关系推断——由 agent 自主决定。

**零基础设施。** 没有数据库，没有服务器，没有后台进程。电脑崩了，你的知识还在，是人类可读的 markdown 文件。

**Agent 原生导航。** 三层渐进式加载意味着 agent 读 ~200 行就能找到所需知识，而不是加载你整个知识库。

**Token 高效设计。** 知识只"编译"一次，之后反复使用。读 brain 条目（~100 行）vs 重新读源材料（~3000+ 行），每次节省 60-80% token。用得越多，省得越多。

**AI 捕获，人决定。** 有价值的综合自动捕获到 inbox/（Karpathy 的复利机制）。但正式知识库（domains/）必须经过用户确认（质量关卡）。

## 对比

| 工具 | 方式 | 代价 |
|------|------|------|
| Claude Code Memory | 扁平 MEMORY.md | 无关系，不可扩展 |
| Obsidian + 手动 | 人工整理 | 对话知识容易遗漏 |
| 向量 DB 插件 | Embedding + 后台 agent | 高内存占用，长时间运行不稳定 |
| RAG 系统 | Embedding + 检索 | 基础设施开销大，不透明 |
| Karpathy LLM-wiki | 自动维护的 markdown wiki | 无学习验证，无挑战系统 |
| AI 教学工具 | 内容推送 + 测验 | 无持久知识图谱，无跨领域迁移 |
| **distill-brain** | **Karpathy 三层架构 + 苏格拉底式学习 + 三层挑战 + 认知模式** | **纯文件，agent 可导航，跨平台** |

## 路线图

- [x] Phase 1：核心循环（`/distill`、`/distill-compile`、`/distill-search`）
- [x] Phase 2：图谱 + 质量 + 分类（`/distill-graph`、`/distill-lint`、`/distill-triage`）
- [x] Phase 3：Obsidian 双视图（YAML + wikilinks）、主动提取建议、跨项目共享
- [x] Phase 4：`/distill-repo`、`/distill-challenge`、Raw Sources 层、自动回写到 inbox
- [ ] Phase 5：多工具支持（Codex、OpenCode、Factory Droid）
- [ ] Phase 6：交互式可视化——结构全景 + 导航回放

## 许可

MIT
