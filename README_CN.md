# distill-brain

一个会教你的第二大脑。基于 [Karpathy 的 LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 架构——同样的三层结构（原始材料、编译后 wiki、schema），但 wiki 不只是用来查的，它是你的导师。

[English README](README.md) · [阅读完整理念文件](https://gist.github.com/JieqLuo/41761c7fbe48b233f6bf6b50ddee5d95)

## 为什么

Karpathy 的 LLM Wiki 解决了一个真实问题：RAG 每次查询都要重新推导，而持久化 wiki 能随时间复利积累。LLM 做所有苦力活——总结、交叉引用、保持一致性——人负责选材料、问好问题。这个思路是对的。

但我一直发现一个现象：我的 wiki 里有很多好条目，但我跟别人解释不出来。LLM 读了论文、写了总结、维护了交叉引用。我搜什么都能搜到组织良好的答案。但当别人问我 wiki 里某个话题时，我发现自己在重新翻阅自己的笔记才能回答。

**Wiki 知道这些东西，我不知道。**

这不是 Karpathy 设计的缺陷——是不同的使用场景。他的 wiki 是一座**图书馆**：建起来是为了查阅。我想要的是一个**导师**：确保我真正理解了放进去的东西。

认知科学里有个被反复验证的现象叫*生成效应*：你自己产出的信息——通过解释、通过挣扎于一个问题、通过调和矛盾——比你被动阅读的信息记得牢得多。*测试效应*同样表明：提取记忆的动作比额外的学习更能强化记忆。distill-brain 把这两者都用上了：LLM 用你已有的知识库作为上下文来挑战你，只有在你证明理解之后才写入条目。

LLM Wiki 是一座有非常好的图书馆员的图书馆。distill-brain 加了一位坐在图书馆里的导师。一本书上架之前，导师问你从中学到了什么。一个想法被确认之前，导师用架子上所有其他书来压力测试它。图书馆照样是图书馆，但你走出去时比走进来时懂得更多。

**图书馆员维护藏书。导师维护你。**

## 安装

```bash
git clone https://github.com/JieqLuo/distill-brain.git ~/.claude/skills/distill-brain
```

不需要依赖，不需要构建，不需要配置。开箱即用。

## 怎么运作

### 两层：广度 + 深度

标准 wiki 模式和导师模式不互斥。它们作为同一系统的两个层级协同工作。

**`inbox/`** 是自动编译层。LLM 捕获跨领域连接、综合笔记、repo 分析草稿。这层长得快，是你的广度——遇到过但没深度处理的东西。

**`domains/`** 是对话门控层。没有经过验证的东西进不来。LLM 在写入前检查你的理解，或者在记录前压力测试你的想法。这层长得慢而刻意，是你的深度——真正内化了的知识。

大多数条目永远不会离开 inbox，这没问题。但对于重要的东西——你会在未来问题中应用的模式、你会在压力下做的决策——第二层确保你做了功课。

### 进入 domains/ 的三条路径（都有质量保证）

| 路径 | 过程 | 条目标记 |
|------|------|---------|
| `/distill` | 你提取并确认 | `confidence_source: extracted` |
| `/distill-learn` | 苏格拉底式提问证明你理解了 | `confidence_source: verified` |
| `/distill-challenge` | 想法经受住三层压力测试 | `confidence_source: challenged` |

### 架构

```
~/.claude/brain/
├── sources/              原始材料 — 不可变
│   ├── articles/         （LLM 只读，永远不改）
│   └── repos/
│
├── domains/              Wiki — 你验证过的知识
│   ├── ai-engineering/   （LLM 维护，需用户确认）
│   ├── learning/
│   └── ...
│
├── inbox/                自动回写缓冲区
│                         （LLM 捕获 → 用户 triage → domains/）
│
├── journal/              提取日志
└── .brain-config.yaml    Schema — 结构和约定
```

**三次读取找到任何知识。** 总索引 → 领域索引 → 条目，共 ~200 行。

## 命令

### 核心循环

```
/distill                    # 从对话中提取知识
/distill-compile            # 重建所有索引
/distill-search {查询词}     # 导航知识库
```

### 学习

```
/distill-learn {主题}        # 苏格拉底式验证——证明你理解了
/distill-challenge {想法}    # 三层压力测试
/distill-repo {URL|路径}     # 通过引导式问答理解代码库
```

`/distill-learn` 不只是保存你说的话。它让你用自己的话解释，追问"为什么"和"怎么做"，用你已有的 brain 条目交叉验证（"你的 brain 里有 X——这个新概念跟它什么关系？"），只有证明真正理解后才写入。

`/distill-challenge` 用你的 brain 作为瞄准方向，但从外部拿弹药。第 1 层：你已有条目中的内部矛盾。第 2 层：盲区攻击——你的 brain 里缺少什么而这个想法依赖什么。第 3 层：你从未接触过的外部视角。不迎合——挑战者必须表态。

`/distill-repo` 渐进式读取代码库（文件树 → 架构 → 关键实现文件），然后通过苏格拉底式问答引导你理解设计决策。之后每次访问节省 60-80% token。

### 维护

```
/distill-graph              # 可视化关系，发现聚类
/distill-lint               # 质量审计（过期、孤立、断链）
/distill-triage             # 处理 inbox → domains
/distill-import [范围]       # 从 Claude Code memory 导入
```

## 存什么

LLM 已经知道教科书内容。再存一遍是零价值的重复。distill-brain 存模型*不知道*的：

- **"我试了 X，发现了 Y"** — 实践验证过的知识
- **"书上说 X，但实际上 Z"** — 被修正的知识
- **"这个框架改变了我做 Y 的方式"** — 被采纳的心智模型
- **"我之前搞错了 X，因为 Y"** — 被更新的认知

随着时间推移，agent 还会观察*你怎么思考*——不只是你知道什么——并存储认知模式，帮助未来的 agent 更好地与你协作。

## 设计理念

**哲学优先于规则。** SKILL.md 不告诉 agent 提取什么，而是给一个思考框架，让它自主判断什么值得提取。

**该硬的地方硬。** 条目格式、质量底线、安全规则不可违反。其余一切——分类、置信度、关系推断——由 agent 自主决定。

**零基础设施。** 没有数据库，没有服务器，没有后台进程。电脑崩了，你的知识还在，是人类可读的 markdown 文件。

**兼容 Obsidian。** 把 brain 路径指向 Obsidian vault 内的文件夹，条目就能在 Obsidian 中浏览，支持完整 `[[双链]]`。条目维护双视图：`related` YAML 给 agent 用 + `## Related` wikilinks 给人看。

## 对比

| 工具 | 方式 | 代价 |
|------|------|------|
| Claude Code Memory | 扁平 MEMORY.md | 无关系，不可扩展 |
| Obsidian + 手动 | 人工整理 | 对话知识容易遗漏 |
| 向量 DB 插件 | Embedding + 后台 agent | 高内存占用，长时间不稳定 |
| RAG 系统 | Embedding + 检索 | 基础设施开销大，不透明 |
| Karpathy LLM-wiki | 自动维护的 markdown wiki | 无学习验证 |
| AI 教学工具 | 内容推送 + 测验 | 无持久知识图谱 |
| **distill-brain** | **三层架构 + 苏格拉底式学习 + 压力测试** | **纯文件，agent 可导航，跨平台** |

## 路线图

- [x] 核心循环（`/distill`、`/distill-compile`、`/distill-search`）
- [x] 图谱 + 质量 + 分类（`/distill-graph`、`/distill-lint`、`/distill-triage`）
- [x] Obsidian 双视图、主动提取建议、跨项目共享
- [x] `/distill-repo`、`/distill-challenge`、Raw Sources 层、自动回写
- [ ] 多工具支持（Codex、OpenCode、Factory Droid）
- [ ] 交互式可视化——结构全景 + 导航回放

## 许可

MIT
