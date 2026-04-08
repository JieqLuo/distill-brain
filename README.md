# distill-brain

Your AI-powered second brain. Distills your unique understanding — not raw knowledge — from conversations, codebases, and practice into a persistent, navigable knowledge base that grows with you.

Built on [Andrej Karpathy's LLM-wiki three-layer architecture](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). Pure markdown, zero background processes, designed for how AI agents actually consume information.

[中文版 README](README_CN.md)

## What it does

Karpathy's LLM-wiki showed that an LLM can maintain a personal knowledge base — auto-ingesting sources, cross-referencing pages, keeping everything consistent. **distill-brain inherits that architecture, then asks a harder question: what knowledge is actually yours?**

LLMs already know textbook content. A wiki that summarizes articles is just re-packaging what the model already has. The real value is what the model *doesn't* have — your judgments, your real-world validations, the gap between what the book says and what actually happened when you tried it.

**distill-brain = Karpathy's three-layer architecture + your processed understanding.** It captures everything (auto write-back to inbox), but what enters your core knowledge base is only what you've verified, challenged, and confirmed through your own thinking.

No vector databases, no embeddings, no background processes. Just markdown files with a three-layer architecture that agents read on demand.

### What makes it different

- **Stores processing, not knowledge.** LLMs already know textbook content. Your brain stores what they don't — your judgments, your real-world validations, the delta between theory and practice.
- **Three-layer architecture.** Raw Sources (immutable evidence) → Wiki (your processed knowledge) → Schema (how the brain works). Based on Karpathy's LLM-wiki design, with auto write-back to inbox for zero-friction knowledge capture.
- **Socratic learning.** `/distill-learn` doesn't just save what you say. It challenges your understanding, tests transfer across domains using your existing brain entries, and only distills once you prove you get it.
- **Three-layer challenge.** `/distill-challenge` uses your brain as targeting context but draws ammunition from outside — finding internal contradictions (Layer 1), attacking blind spots (Layer 2), and introducing perspectives you've never encountered (Layer 3).
- **Repo comprehension.** `/distill-repo` helps you genuinely understand a codebase through guided Q&A — then "compiles" the knowledge so you never pay the full token cost again (60-80% savings on every subsequent access).
- **Cognitive patterns.** Over time, the agent observes how you think — not just what you know — and stores distilled patterns that help future agents collaborate with you better.
- **Platform-agnostic brain.** Pure markdown files. Works with Obsidian, any text editor, or any AI tool that can read files. Your brain is yours, not locked to any platform.

## Architecture

```
                    Karpathy's Three Layers
                    ──────────────────────

~/.claude/brain/
├── sources/              RAW SOURCES — immutable original materials
│   ├── articles/         (LLM reads, never modifies)
│   └── repos/
│
├── domains/              WIKI — your processed knowledge
│   ├── ai-engineering/   (LLM maintains with user approval)
│   ├── learning/         
│   └── ...               
│
├── inbox/                Auto write-back buffer
│                         (LLM captures → user triages → domains/)
│
├── journal/              Extraction logs
└── .brain-config.yaml    SCHEMA — structure and conventions
                          (along with SKILL.md)
```

**Three reads to find anything.** Root INDEX → domain INDEX → entry. ~200 lines total vs loading your entire brain.

**Auto write-back.** Valuable query results are automatically captured to `inbox/`. User runs `/distill-triage` to review: accept the draft as-is (fast) or re-process from source (thorough). Karpathy's speed + distill-brain's quality.

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

Processes entries in `inbox/` — both manually submitted and auto-captured drafts. Proposes domain placement, infers relationships, and moves them into the brain structure after your confirmation.

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

Socratic learning mode. Probes your understanding with why/how questions, tests transfer across domains using your existing brain entries, challenges weak spots and contradictions. Only distills once you demonstrate genuine comprehension. Entries get `confidence_source: verified`.

### Pressure-test an idea

```
/distill-challenge {idea or topic}
```

Three-layer challenge mode. Layer 1: finds contradictions within your existing brain entries. Layer 2: attacks blind spots (what's missing from your brain). Layer 3: introduces perspectives from outside your knowledge graph entirely. No sycophancy — the challenger takes positions and states what evidence would change its mind. Surviving ideas get `confidence_source: challenged`.

### Understand a codebase

```
/distill-repo {url or local path}
```

Progressive repo analysis: file tree + README → architecture docs → key files. Guides you through Socratic Q&A to genuinely understand the design decisions. Stores both an architecture reference (always, since code may not be in training data) and your verified insights. Saves 60-80% tokens on every subsequent access.

## Where knowledge lives

By default, your knowledge base is stored at `~/.claude/brain/` as plain markdown files. You can configure the path in `.brain-config.yaml`.

**Obsidian compatible.** Point the brain path to a folder inside your Obsidian vault, and your knowledge entries become browsable, searchable, and linkable in Obsidian — with full `[[wikilink]]` support. Entries maintain dual relationship views: `related` YAML for agents + `## Related` wikilinks for humans.

## Design Principles

**Philosophy over rules.** The SKILL.md doesn't tell the agent *what* to extract — it gives a thinking framework for *judging* what's worth extracting. The agent decides.

**Hard boundaries where they matter.** Entry format, quality floor, and safety rules are non-negotiable. Everything else — classification, confidence, relationships — is the agent's call.

**Zero infrastructure.** No databases, no servers, no background processes. If your computer crashes, your knowledge is still there in readable markdown files.

**Agent-native navigation.** Three-layer progressive disclosure means agents load ~200 lines to find what they need, not your entire brain.

**Token-efficient by design.** Knowledge is "compiled" once and reused. Reading a brain entry (~100 lines) vs re-reading source material (~3000+ lines) saves 60-80% tokens on every subsequent access. The more you use it, the more you save.

**AI captures, human decides.** Valuable syntheses are auto-captured to inbox/ (Karpathy's compounding). But nothing enters your formal knowledge base (domains/) without your confirmation (quality gate).

## Comparison

| Tool | Approach | Trade-off |
|------|----------|-----------|
| Claude Code Memory | Flat MEMORY.md | No relationships, doesn't scale |
| Obsidian + manual | Human-curated vault | Conversations slip through |
| Vector DB plugins | Embeddings + background agents | High memory usage, crashes over time |
| RAG systems | Embeddings + retrieval | Infrastructure overhead, opaque |
| Karpathy LLM-wiki | Auto-maintained markdown wiki | No learning verification, no challenge system |
| AI tutoring tools | Content delivery + quizzes | No persistent knowledge graph, no cross-domain transfer |
| **distill-brain** | **Karpathy's three-layer architecture + Socratic learning + three-layer challenge + cognitive patterns** | **Pure files, agent-navigable, platform-agnostic** |

## Roadmap

- [x] Phase 1: Core loop (`/distill`, `/distill-compile`, `/distill-search`)
- [x] Phase 2: Graph + quality + triage (`/distill-graph`, `/distill-lint`, `/distill-triage`)
- [x] Phase 3: Obsidian dual-view (YAML + wikilinks), proactive distill suggestions, cross-project sharing
- [x] Phase 4: `/distill-repo`, `/distill-challenge`, Raw Sources layer, auto write-back to inbox
- [ ] Phase 5: Multi-tool support (Codex, OpenCode, Factory Droid)
- [ ] Phase 6: Interactive visualization — structure overview + live navigation replay

## License

MIT
