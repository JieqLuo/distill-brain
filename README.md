# distill-brain

AI-Native personal knowledge management for Claude Code.

Turn your conversations into a structured, navigable knowledge base. Pure markdown, zero background processes, designed for how AI agents actually consume information.

## What it does

`distill-brain` extracts transferable knowledge from your Claude Code sessions — decisions, patterns, insights, troubleshooting — and organizes them into a layered markdown knowledge base that agents can navigate efficiently.

**Not another RAG system.** No vector databases, no embeddings, no background processes. Just markdown files with a three-layer index that agents read on demand.

## How it works

```
Your conversations
       ↓  /kb-extract (you confirm what's worth saving)
~/.claude/kb/
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
# Clone into your Claude Code skills directory
git clone https://github.com/YOUR_USERNAME/distill-brain.git ~/.claude/skills/distill-brain
```

That's it. No dependencies. No build step. No configuration required.

## Usage

### Extract knowledge from a conversation

```
/kb-extract
```

Reviews the current conversation, identifies transferable knowledge, and presents candidates for your approval. Nothing is saved without your explicit confirmation.

### Rebuild indexes

```
/kb-compile
```

Regenerates all INDEX.md files from current entries. Run after manual edits or imports.

### Search the knowledge base

```
/kb-search {query}
```

Agent navigates the three-layer index to find relevant knowledge. Max 5 file reads.

## Design Principles

**Philosophy over rules.** The SKILL.md doesn't tell the agent *what* to extract — it gives a thinking framework for *judging* what's worth extracting. The agent decides.

**Hard boundaries where they matter.** Entry format, quality floor, and safety rules are non-negotiable. Everything else — classification, confidence, relationships — is the agent's call.

**Zero infrastructure.** No databases, no servers, no background processes. If your computer crashes, your knowledge is still there in readable markdown files.

**Agent-native navigation.** Three-layer progressive disclosure means agents load ~200 lines to find what they need, not your entire knowledge base.

## Knowledge Entry Format

```yaml
---
title: Entry title
domain: ai-engineering
created: 2026-04-07
source: session:my-project:2026-04-07
tags: [pattern, skill-design]
---

# Entry Title

> **Core insight:** One sentence summary for INDEX display.

(rest of entry — structure decided by agent based on content type)
```

## Comparison

| Tool | Approach | Trade-off |
|------|----------|-----------|
| Claude Code Memory | Flat MEMORY.md | No relationships, doesn't scale |
| Obsidian + manual | Human-curated vault | Conversations slip through |
| claude-mem | Vector DB + background agents | 8GB RAM, crashes after 10h |
| RAG systems | Embeddings + retrieval | Infrastructure overhead, opaque |
| **distill-brain** | **Layered markdown index** | **Pure files, agent-navigable** |

## Roadmap

- [x] Phase 1: Core loop (`/kb-extract`, `/kb-compile`, `/kb-search`)
- [ ] Phase 2: Relationship graph + quality (`/kb-graph`, `/kb-lint`)
- [ ] Phase 3: Obsidian sync, auto-extraction, cross-project sharing

## Inspiration

- [Andrej Karpathy](https://x.com/karpathy) — LLM Knowledge Bases: using LLMs to build personal knowledge bases
- [Graphify](https://github.com/safishamsi/graphify) — confidence scoring, typed relationships, community detection
- Built from daily practice with Claude Code + Obsidian knowledge management

## License

MIT
