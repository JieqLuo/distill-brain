# distill-brain

A second brain that teaches you back. Built on [Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) architecture — same three layers (raw sources, compiled wiki, schema), but the wiki isn't just a reference. It's a tutor.

[中文版 README](README_CN.md) · [Read the idea file](https://gist.github.com/JieqLuo/41761c7fbe48b233f6bf6b50ddee5d95)

## Why

Karpathy's LLM Wiki solved a real problem: RAG re-derives knowledge on every query, while a persistent wiki compounds over time. The LLM does the bookkeeping — summarizing, cross-referencing, maintaining consistency — and the human curates sources and asks good questions. This works.

But I kept noticing something. My wiki had great entries that I couldn't explain to anyone. The LLM had read the papers, written the summaries, maintained the cross-references. I could search for anything and get a well-organized answer. But when someone asked me about a topic in my own wiki, I'd find myself re-reading my own pages to answer them.

**The wiki knew things. I didn't.**

This isn't a flaw in Karpathy's design — it's a different use case. His wiki is a **library**: you build it to look things up. What I wanted was a **tutor**: something that makes sure I understand what goes into it.

In cognitive science, the *generation effect* shows that information you produce yourself — by explaining, by struggling with a question, by reconciling contradictions — is retained far better than information you passively read. The *testing effect* shows that retrieval strengthens memory more than additional study. distill-brain puts both to work: the LLM challenges you using your own knowledge base as context, and only writes the entry after you demonstrate understanding.

The LLM Wiki is a library with a very good librarian. distill-brain adds a tutor who sits in the library. Before a book goes on the shelf, the tutor asks you what you learned from it. Before you commit to an idea, the tutor stress-tests it using everything else on the shelves. The library still works as a library. But you walk out knowing more than you walked in with.

**The librarian maintains the collection. The tutor maintains *you*.**

## Install

```bash
git clone https://github.com/JieqLuo/distill-brain.git ~/.claude/skills/distill-brain
```

No dependencies. No build step. No configuration required. Works with Claude Code out of the box.

## How it works

### Two tiers: breadth + depth

The standard wiki pattern and the tutor pattern aren't mutually exclusive. They work as two tiers of the same system.

**`inbox/`** is auto-compiled. The LLM captures cross-domain connections, synthesis notes, repo analysis drafts. This layer grows fast. It's your breadth — things you've encountered but haven't deeply processed.

**`domains/`** is dialogue-gated. Nothing enters without verification. The LLM checks your understanding before writing the entry, or pressure-tests your idea before recording it. This layer grows slowly and deliberately. It's your depth — knowledge you've actually internalized.

Most entries never leave inbox. That's fine. But for the things that matter — the patterns you'll apply to future problems, the decisions you'll make under pressure — the second tier makes sure you've done the work.

### Three paths into domains/ (all quality-guaranteed)

| Path | What happens | Entry gets |
|------|-------------|-----------|
| `/distill` | You extract and confirm | `confidence_source: extracted` |
| `/distill-learn` | Socratic questioning proves you understand | `confidence_source: verified` |
| `/distill-challenge` | Idea survives three-layer pressure testing | `confidence_source: challenged` |

### Architecture

```
~/.claude/brain/
├── sources/              RAW SOURCES — immutable originals
│   ├── articles/         (LLM reads, never modifies)
│   └── repos/
│
├── domains/              WIKI — your verified knowledge
│   ├── ai-engineering/   (LLM maintains with user approval)
│   ├── learning/
│   └── ...
│
├── inbox/                Auto write-back buffer
│                         (LLM captures → user triages → domains/)
│
├── journal/              Extraction logs
└── .brain-config.yaml    SCHEMA — structure and conventions
```

**Three reads to find anything.** Root INDEX → domain INDEX → entry. ~200 lines total vs loading your entire brain.

## Commands

### Core loop

```
/distill                    # Extract knowledge from conversation
/distill-compile            # Rebuild all indexes
/distill-search {query}     # Navigate the knowledge base
```

### Learning

```
/distill-learn {topic}      # Socratic verification — prove you understand
/distill-challenge {idea}   # Three-layer pressure test
/distill-repo {url|path}    # Understand a codebase through guided Q&A
```

`/distill-learn` doesn't just save what you say. It asks you to explain in your own words, probes with "why" and "how" questions, cross-references your existing brain entries ("Your brain has X — how does this new concept relate?"), and only distills once you demonstrate comprehension.

`/distill-challenge` uses your brain as targeting context but draws ammunition from outside. Layer 1: internal contradictions in your existing entries. Layer 2: blind spot attack — what's absent from your brain that the idea depends on. Layer 3: outside perspectives you've never encountered. No sycophancy — the challenger takes positions.

`/distill-repo` reads a codebase progressively (file tree → architecture → key implementation files), then guides you through Socratic Q&A to understand the design decisions. Saves 60-80% tokens on every subsequent access.

### Maintenance

```
/distill-graph              # Visualize relationships, find clusters
/distill-lint               # Audit quality (stale, orphans, broken links)
/distill-triage             # Process inbox → domains
/distill-import [scope]     # Import from Claude Code memory
```

## What it stores

LLMs already know textbook content. Storing it again is duplication with zero value. distill-brain stores what the model *doesn't* have:

- **"I tried X and found Y"** — practice-validated knowledge
- **"Author says X, but in my experience Z"** — corrected knowledge
- **"This framework changed how I approach Y"** — adopted mental models
- **"I was wrong about X because Y"** — updated beliefs

Over time, the agent also observes *how you think* — not just what you know — and stores cognitive patterns that help future agents collaborate with you better.

## Design principles

**Philosophy over rules.** The SKILL.md gives a thinking framework for judging what's worth extracting. The agent decides.

**Hard boundaries where they matter.** Entry format, quality floor, and safety rules are non-negotiable. Everything else — classification, confidence, relationships — is the agent's call.

**Zero infrastructure.** No databases, no servers, no background processes. If your computer crashes, your knowledge is still there in readable markdown files.

**Obsidian compatible.** Point the brain path to a folder inside your Obsidian vault, and entries become browsable with full `[[wikilink]]` support. Entries maintain dual relationship views: `related` YAML for agents + `## Related` wikilinks for humans.

## Comparison

| Tool | Approach | Trade-off |
|------|----------|-----------|
| Claude Code Memory | Flat MEMORY.md | No relationships, doesn't scale |
| Obsidian + manual | Human-curated vault | Conversations slip through |
| Vector DB plugins | Embeddings + background agents | High memory usage, crashes over time |
| RAG systems | Embeddings + retrieval | Infrastructure overhead, opaque |
| Karpathy LLM-wiki | Auto-maintained markdown wiki | No learning verification |
| AI tutoring tools | Content delivery + quizzes | No persistent knowledge graph |
| **distill-brain** | **Three-layer architecture + Socratic learning + pressure testing** | **Pure files, agent-navigable, platform-agnostic** |

## Roadmap

- [x] Core loop (`/distill`, `/distill-compile`, `/distill-search`)
- [x] Graph + quality + triage (`/distill-graph`, `/distill-lint`, `/distill-triage`)
- [x] Obsidian dual-view, proactive distill suggestions, cross-project sharing
- [x] `/distill-repo`, `/distill-challenge`, raw sources layer, auto write-back
- [ ] Multi-tool support (Codex, OpenCode, Factory Droid)
- [ ] Interactive visualization — structure overview + live navigation replay

## License

MIT
