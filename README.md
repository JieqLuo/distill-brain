# distill-brain

Your AI-powered second brain. Distills your unique understanding — not raw knowledge — from conversations, codebases, and practice into a persistent, navigable knowledge base that grows with you.

Inspired by [Andrej Karpathy's LLM-wiki vision](https://x.com/karpathy). Pure markdown, zero background processes, designed for how AI agents actually consume information.

[中文版 README](README_CN.md)

## What it does

`distill-brain` extracts transferable knowledge from your Claude Code sessions — decisions, patterns, insights, troubleshooting — and organizes them into a layered markdown knowledge base that agents can navigate efficiently.

**Not another RAG system.** No vector databases, no embeddings, no background processes. Just markdown files with a three-layer index that agents read on demand.

### What makes it different

- **Stores processing, not knowledge.** LLMs already know textbook content. Your brain stores what they don't — your judgments, your real-world validations, the delta between theory and practice.
- **Socratic learning.** `/distill-learn` doesn't just save what you say. It challenges your understanding, tests transfer across domains, and only distills once you prove you get it.
- **Repo comprehension.** `/distill-repo` helps you genuinely understand a codebase through guided Q&A — then "compiles" the knowledge so you never pay the full token cost again (60-80% savings on every subsequent access).
- **Cognitive patterns.** Over time, the agent observes how you think — not just what you know — and stores distilled patterns that help future agents collaborate with you better.
- **Platform-agnostic brain.** Pure markdown files. Works with Obsidian, any text editor, or any AI tool that can read files. Your brain is yours, not locked to any platform.

## How it works

```
Your conversations
       ↓  /distill (you confirm what's worth saving)
~/.claude/brain/
├── INDEX.md              ← agent reads this first (~50 lines)
├── domains/
│   ├── ai-engineering/
│   │   ├── INDEX.md      ← then narrows to a domain (~30 lines)
│   │   └── patterns.md   ← then reads specific entries
│   └── tools/
├── inbox/
└── journal/
```

**Three reads to find anything.** vs loading your entire knowledge base into context.

## Install

```bash
git clone https://github.com/JieqLuo/distill-brain.git ~/.claude/skills/distill-brain
```

That's it. No dependencies. No build step. No configuration required.

## Usage

### Extract knowledge from a conversation

```
/distill
```

Reviews the current conversation, identifies transferable knowledge, and presents candidates for your approval. Nothing is saved without your explicit confirmation.

### Rebuild indexes

```
/distill-compile
```

Regenerates all INDEX.md files from current entries. Run after manual edits or imports.

### Search the knowledge base

```
/distill-search {query}
```

Agent navigates the three-layer index to find relevant knowledge. Max 5 file reads.

### Visualize the knowledge graph

```
/distill-graph
```

Scans all entries, builds a relationship graph, identifies hub nodes and clusters. Outputs a markdown report (`journal/graph-{date}.md`) and an interactive HTML visualization (`graph.html`) you can open in any browser.

### Audit knowledge quality

```
/distill-lint
```

Checks for stale entries, orphans, broken links, and duplicates. Presents findings with suggested fixes for your approval.

### Triage inbox entries

```
/distill-triage
```

Processes entries in `inbox/` — proposes domain placement, infers relationships, and moves them into the brain structure after your confirmation.

### Import from Claude Code memory

```
/distill-import           # current project's memory
/distill-import all       # scan all projects
/distill-import {path}    # specific memory directory
```

Reads `MEMORY.md` and memory files, filters for transferable cross-project knowledge, and imports qualifying entries into the brain. Project-specific rules stay in auto memory.

### Learn with verified understanding

```
/distill-learn {topic}
```

Socratic learning mode. Instead of passively storing what you say, the agent probes your understanding — asks why/how questions, tests transfer to other domains using your existing brain entries, and challenges weak spots. Only distills once you demonstrate genuine comprehension. Entries get `confidence_source: verified`.

### Pressure-test an idea

```
/distill-challenge {idea or topic}
```

Three-layer challenge mode. Uses your brain as targeting context but draws ammunition from outside your knowledge. Layer 1: finds contradictions within your existing entries. Layer 2: attacks blind spots (what's missing from your brain). Layer 3: introduces perspectives from outside your knowledge graph entirely. No sycophancy — the challenger takes positions and states what evidence would change its mind. Surviving ideas get `confidence_source: challenged`.

## Where knowledge lives

By default, your knowledge base is stored at `~/.claude/brain/` as plain markdown files. You can configure the path in `.brain-config.yaml`.

**Obsidian compatible.** Point the brain path to a folder inside your Obsidian vault, and your knowledge entries become browsable, searchable, and linkable in Obsidian — with full `[[wikilink]]` support.

## Design Principles

**Philosophy over rules.** The SKILL.md doesn't tell the agent *what* to extract — it gives a thinking framework for *judging* what's worth extracting. The agent decides.

**Hard boundaries where they matter.** Entry format, quality floor, and safety rules are non-negotiable. Everything else — classification, confidence, relationships — is the agent's call.

**Zero infrastructure.** No databases, no servers, no background processes. If your computer crashes, your knowledge is still there in readable markdown files.

**Agent-native navigation.** Three-layer progressive disclosure means agents load ~200 lines to find what they need, not your entire knowledge base.

**Token-efficient by design.** Knowledge is "compiled" once and reused. Reading a brain entry (~100 lines) vs re-reading source material (~3000+ lines) saves 60-80% tokens on every subsequent access. The more you use it, the more you save.

## Knowledge Entry Format

```yaml
---
title: Three-Layer Progressive Navigation
domain: ai-engineering
created: 2026-04-07
source: session:distill-brain:2026-04-07
tags: [information-architecture, token-efficiency]
---

# Three-Layer Progressive Navigation

> **Core insight:** Root index → domain index → entry. Three reads to find anything, ~200 lines total vs loading the entire knowledge base.

(rest of entry — structure decided by agent based on content type)
```

## Comparison

| Tool | Approach | Trade-off |
|------|----------|-----------|
| Claude Code Memory | Flat MEMORY.md | No relationships, doesn't scale |
| Obsidian + manual | Human-curated vault | Conversations slip through |
| Vector DB plugins | Embeddings + background agents | High memory usage, crashes over time |
| RAG systems | Embeddings + retrieval | Infrastructure overhead, opaque |
| AI tutoring tools | Content delivery + quizzes | No persistent knowledge graph, no cross-domain transfer |
| **distill-brain** | **Layered markdown index + Socratic learning + cognitive patterns** | **Pure files, agent-navigable, platform-agnostic** |

## Roadmap

- [x] Phase 1: Core loop (`/distill`, `/distill-compile`, `/distill-search`)
- [x] Phase 2: Graph + quality + triage (`/distill-graph`, `/distill-lint`, `/distill-triage`)
- [x] Phase 3: Obsidian dual-view (YAML + wikilinks), proactive distill suggestions, cross-project sharing
- [ ] Phase 4: `/distill-repo` (extract patterns from codebases), multi-tool support (Codex, OpenCode, Factory Droid)
- [ ] Phase 5: Interactive visualization — structure overview (domain density, hub nodes, orphans) + live navigation replay (watch agent traverse the brain in real-time)

## License

MIT
