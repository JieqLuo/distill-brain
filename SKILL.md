---
name: distill-brain
description: >
  AI-Native personal knowledge management. Distills transferable knowledge from conversations
  into a layered, navigable markdown knowledge base.
  Use when: user says /distill, /distill-compile, /distill-search, /distill-graph, /distill-lint, /distill-triage, /distill-import, /distill-learn, /distill-challenge, /distill-repo, or asks to save/organize/find knowledge.
---

# distill-brain

A personal knowledge base that you build and navigate. Pure markdown, zero background processes.

## Strategy Philosophy

You are building a **second brain** for the user — a persistent, structured knowledge base that makes future conversations smarter. Your role is not to follow extraction rules, but to exercise judgment about what knowledge is worth preserving.

### How to think about extraction

**Transferability test:** "If the user encountered a similar situation in 3 months, in a different project, would having this knowledge change their behavior?" If yes, it belongs in the brain. If it's only useful in the current context, it belongs in session memory or project-level CLAUDE.md.

**Information routing:**
- Temporary state, current task progress → stays in conversation (don't save)
- Project-specific operational rules → Claude Code auto memory (MEMORY.md)
- Transferable knowledge, patterns, decisions, insights → **distill-brain**
- Long-form content, research, articles → Obsidian vault

**Relationship awareness:** When you encounter new knowledge, consider how it connects to what already exists in the brain. Does it extend something? Contradict something? Depend on something? These connections are often more valuable than the knowledge itself.

**Quality over quantity:** One well-articulated pattern is worth more than ten shallow observations. If you're unsure whether something meets the bar, it probably doesn't.

### Cognitive patterns — the third layer

The brain stores two kinds of content:
1. **What the user knows** — knowledge entries (patterns, decisions, insights, concepts)
2. **How the user thinks** — cognitive patterns (thinking tendencies, blind spots, strengths, learning style)

Cognitive patterns use `type: cognitive-pattern` and belong in the `self/` domain. They are NOT raw single-conversation observations — they are distilled patterns confirmed across multiple interactions.

**When to extract a cognitive pattern:**
- You've observed the same thinking tendency across 3+ conversations or situations
- The pattern is stable enough to be useful (not a one-off mood or context effect)
- Knowing this pattern would help a future agent collaborate better with this user

**What a cognitive pattern entry looks like:**
- Core insight describes the pattern objectively (observation, not judgment)
- Body includes: evidence (when was this observed), impact (how it affects outcomes), and growth angle (how awareness of this could help)
- Confidence starts lower (`0.6-0.7`) since cognitive patterns are harder to verify than knowledge

**Critical rule:** Cognitive patterns inform how you INTERACT with the user (communication style, when to challenge, when to support). They must NEVER be used to bias knowledge suggestions — if the user tends to favor simplicity, that's a reason to CHALLENGE them with complexity when appropriate, not to filter it out.

### Two-tier knowledge: inbox (LLM wiki) + domains (your confirmed knowledge)

The brain has two knowledge tiers, like how your mind has "things you know" vs "things you've verified through experience":

- **`inbox/`** — Karpathy's wiki layer. LLM auto-captures valuable syntheses here: cross-domain connections, query results, repo analysis drafts. Grows fast, no human confirmation needed. `confidence_source: auto-captured`.
- **`domains/`** — Your confirmed knowledge. Only entries that have been verified enter here.

**Three paths into domains/ (all quality-guaranteed):**
1. `/distill` — you manually extract and confirm
2. `/distill-learn` → `confidence_source: verified` — you proved you understand through Socratic questioning
3. `/distill-challenge` → `confidence_source: challenged` — the idea survived three-layer pressure testing

**Auto-captured entries stay in inbox/.** They don't auto-promote to domains/. The user runs `/distill-triage` when they want to review inbox entries — each one requires human judgment to enter domains/. No batch shortcuts, no auto-promotion. Quality requires human eyes, same as Karpathy.

**inbox is not a to-do list to clear.** It's a living layer of unverified knowledge. It's there when you need it, searchable, but doesn't pollute domains/. Most entries may never leave inbox — and that's fine.

**What to auto-capture to inbox:**
- Query results that synthesize multiple brain entries into new insight
- Cross-domain connections discovered during `/distill-search`
- Valuable analysis produced during `/distill-repo` or `/distill-challenge`

**What NOT to auto-capture:**
- Routine answers that don't produce new knowledge
- Content that already exists in domains/
- Temporary task-specific output

### When to suggest /distill

You should proactively suggest `/distill` when **both** conditions are met:
1. The conversation has produced transferable knowledge (passes the transferability test)
2. That knowledge is at risk of being lost

Risk signals (any one is enough):
- The user is about to `/clear` or switch tasks
- Context is being compacted (you notice prior messages being compressed)
- The conversation has been long and substantial — context is filling up, and your reasoning quality may degrade soon
- A major topic or task just concluded naturally

**How to suggest:** Brief, non-intrusive. One line: "This conversation had some insights worth keeping — want me to run `/distill`?" Don't over-explain or list candidates unprompted. If the user says no, move on.

## Hard Constraints

These rules cannot be overridden. They exist because the system breaks without them.

### Structure

- Every entry MUST have YAML frontmatter with at minimum: `title`, `domain`, `created`, `source`, `tags`
- Every entry MUST have a `> **Core insight:**` blockquote after the title heading — INDEX generation depends on it
- INDEX.md files are auto-generated by `/distill-compile` — never edit them manually
- Directory structure is fixed: `domains/`, `inbox/`, `journal/`, `sources/` under the brain root
- Files in `sources/` are **immutable** — the LLM reads from them but never modifies them

### Quality floor

- Never store code snippets (they belong in source code or git)
- Never store facts easily found via search engine
- Never store temporary task progress
- Never store duplicates — Grep existing entries before creating new ones
- Every entry must have a `source` field traceable to where the knowledge came from
- Extraction requires user confirmation — never silently write to the brain

### Safety

- Never store API keys, passwords, tokens, or secrets
- Never store other people's private information

### Everything else — extraction judgment, classification, confidence scoring, relationship inference, entry structure, section depth — is yours to decide.

## Factual Reference

### Brain Location

Default: `~/.claude/brain/`. Check `.brain-config.yaml` for user overrides.

```
~/.claude/brain/
├── INDEX.md              # Layer 0: root navigation
├── domains/              # WIKI LAYER — your processed knowledge (LLM-maintained with user approval)
│   ├── INDEX.md          # Layer 1: domain directory
│   └── {domain-name}/
│       ├── INDEX.md      # Layer 2: domain entries
│       └── {entry}.md    # individual knowledge entries
├── sources/              # RAW SOURCES — immutable original materials (LLM reads, never modifies)
│   ├── INDEX.md
│   ├── articles/         # saved articles, papers, notes
│   └── repos/            # /distill-repo analysis snapshots
├── inbox/                # extracted but untriaged entries
│   └── INDEX.md
├── journal/              # extraction session logs
└── .brain-config.yaml    # SCHEMA LAYER — configuration (along with SKILL.md)
```

**Three-layer architecture** (inspired by [Karpathy's LLM-wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)):
- **Raw Sources** (`sources/`): Immutable original materials. LLM reads but never modifies.
- **Wiki** (`domains/`): Your processed knowledge. LLM maintains with user approval.
- **Schema** (SKILL.md + `.brain-config.yaml`): How the brain is structured and maintained.

### Entry Schema

**Required fields** (INDEX generation and quality floor depend on these):

```yaml
---
title: Entry title
domain: domain-name
created: YYYY-MM-DD
source: session:{project}:{date} | manual | import:{origin} | sources/{path}
tags: [tag1, tag2]
---

# Entry Title

> **Core insight:** One sentence that lets an agent decide whether to read further.
```

**Optional fields** (add when they provide value, skip when they don't):

```yaml
type: pattern | decision | concept | troubleshooting | insight | reference
confidence: 0.0-1.0
confidence_source: extracted | inferred | ambiguous
updated: YYYY-MM-DD
related:
  - id: other-entry-filename
    relation: extends | contradicts | depends-on | similar | applied-in
    strength: 0.0-1.0
supersedes: [replaced-entry-filenames]
```

Entry body structure is not fixed. Choose sections that fit the knowledge type.

### Dual Relationship View

Relationships are expressed in two places simultaneously — one for agents, one for humans:

1. **`related` YAML in frontmatter** — structured, typed, with strength scores. This is what agents read for navigation, graph building, and relationship inference.
2. **`## Related` section at the end of entry body** — `[[wikilinks]]` with relationship labels. This is what humans see when browsing in Obsidian or any markdown viewer.

Both views MUST stay in sync. When writing or updating an entry with relationships, always write both. Example:

```markdown
---
related:
  - id: progressive-knowledge-navigation
    relation: applied-in
    strength: 0.8
---

# Entry Title

> **Core insight:** ...

(body)

## Related

- [[progressive-knowledge-navigation]] — applied-in (0.8)
```

`/distill-compile` checks consistency between the two views and flags mismatches.

### Navigation Protocol

When you need to find knowledge in the brain:

1. Read `INDEX.md` at the brain root (~50 lines). This tells you which domains exist and what's recent.
2. Read `domains/{relevant-domain}/INDEX.md` (~20-50 lines). Each entry has its core insight one-liner.
3. Read specific entry files only when needed. Follow `related` links at most 2 hops.

Budget: 5 file reads maximum per knowledge lookup. Never scan the entire brain.

### INDEX.md Format

Root INDEX contains:
- Domain table: name, description, entry count, last updated
- Recent additions (last 7 days): entry title + core insight
- Inbox count (if any pending)

Domain INDEX contains:
- Entries grouped by type (if type field exists) or flat list
- Each entry: filename, title, core insight one-liner

### First Run

If `~/.claude/brain/` does not exist, create it with this structure:

```
~/.claude/brain/
├── INDEX.md              (empty, will be populated by /distill-compile)
├── domains/
│   └── INDEX.md          (empty)
├── sources/
│   ├── INDEX.md          (empty)
│   ├── articles/
│   └── repos/
├── inbox/
│   └── INDEX.md          (empty)
├── journal/
└── .brain-config.yaml
```

Default `.brain-config.yaml`:

```yaml
brain_path: ~/.claude/brain
staleness_days:
  reference: 90
  concept: 180
  pattern: 365
  troubleshooting: 90
  insight: 180
```

## Commands

### /distill

**Goal:** Identify transferable knowledge from the current conversation and save it to the brain with user approval.

**Must do:**
1. Read root INDEX.md to know what already exists
2. Apply the transferability test and quality floor to identify candidates
3. Present candidates to the user with: proposed title, domain, core insight, and why it's worth saving
4. Wait for user confirmation (accept all, select specific ones, edit, or reject)
5. Write confirmed entries to `domains/{domain}/` or `inbox/` if domain is unclear
6. Run `/distill-compile` to update indexes

**Your call:** What to extract, how to classify it, what confidence to assign, what relationships to infer, whether to create a new domain, how to structure the entry body.

### /distill-compile

**Goal:** Regenerate all INDEX.md files from current entries.

**Must do:**
1. Scan all `domains/*/` directories
2. Read frontmatter + core insight blockquote from each entry
3. Regenerate root INDEX.md and each domain INDEX.md

**Your call:** Sort order, whether to flag stale entries, whether to suggest domain reorganization.

### /distill-search {query}

**Goal:** Find relevant knowledge efficiently using the navigation protocol.

**Must do:**
1. Follow the navigation protocol (root INDEX → domain INDEX → entry, max 5 reads)

**Your call:** Which domains to check, whether to Grep for additional matches, how many entries to return, whether to follow relationship links.

### /distill-graph

**Goal:** Build a knowledge graph from all entries, discover clusters and hub nodes, output a readable report and an interactive visualization.

**Must do:**
1. Scan all entries across all `domains/*/` — read frontmatter (`tags`, `related`) and core insight
2. Build adjacency: explicit edges from `related` fields (bidirectional) + implicit edges from tag overlap
3. Identify hub nodes: entries with the most inbound references = knowledge pillars
4. Detect clusters: groups of entries densely connected by relationships and shared tags
5. Output two files:
   - `journal/graph-{date}.md` — markdown report: hub nodes, clusters, orphan entries (no relations, unique tags), suggested new relationships
   - `graph.html` at brain root — self-contained interactive visualization (inline JS, no external dependencies). Nodes = entries, edges = relationships, color = domain. Openable by double-clicking in any browser.

**Your call:** Clustering algorithm details, edge weight calculation, visualization layout, whether to suggest relationship additions or domain reorganization, level of detail in the report, Mermaid diagram inclusion in the markdown report.

### /distill-lint

**Goal:** Audit the brain for quality issues — stale entries, contradictions, duplicates, orphans, broken links — and suggest fixes.

**Must do:**
1. Scan all entries across all `domains/*/` — read frontmatter and core insight
2. Check staleness: compare `created` (or `updated`) against thresholds in `.brain-config.yaml`
3. Check orphans: entries with no `related` field and no inbound references from other entries
4. Check broken links: `related` fields that reference non-existent entry filenames
5. Present findings as a categorized report with suggested actions
6. Wait for user confirmation before making any changes

**Your call:** Whether to also check for near-duplicate entries (similar titles/tags/insights), whether to flag contradictions between entries, severity ranking of issues, which fixes to auto-apply vs which need manual review.

### /distill-triage

**Goal:** Process `inbox/` entries — assign them to domains, infer relationships, and move them into the brain structure.

**Must do:**
1. Read `inbox/INDEX.md` and all entries in `inbox/`
2. Read root INDEX.md to understand existing domain structure
3. For each inbox entry: propose a target domain (existing or new) and potential relationships
4. Present triage plan to user for confirmation
5. Move confirmed entries to `domains/{domain}/`, updating frontmatter as needed
6. Run `/distill-compile` to update indexes

**Your call:** Whether an entry fits an existing domain or warrants a new one, what relationships to infer, whether to merge inbox entries that cover the same topic, confidence scoring for inferred relationships.

### /distill-import {scope}

**Goal:** Migrate knowledge from Claude Code auto memory (`MEMORY.md` + memory files) into the brain, filtering for entries that meet the transferability test.

**Scope argument:**
- No argument or `current` — import from the current project's memory (`~/.claude/projects/{current-project}/memory/`)
- `all` — scan all projects under `~/.claude/projects/*/memory/`
- A path — import from a specific memory directory (e.g., `~/.claude/projects/-Users-jl-Desktop-my-project/memory/`)

**Must do:**
1. Locate memory files based on scope. Read `MEMORY.md` index and each referenced memory file.
2. Apply the transferability test: only import entries that contain cross-project knowledge (patterns, decisions, insights). Skip project-specific operational rules, ephemeral state, and task progress — those belong in auto memory.
3. Present import candidates to user with: source project, proposed title, domain, core insight, and what will NOT be imported (with reasons)
4. Wait for user confirmation
5. Write confirmed entries to `domains/{domain}/` or `inbox/`, with `source: import:memory:{project-name}` in frontmatter
6. Run `/distill-compile` to update indexes

**Your call:** Which memory entries qualify as transferable knowledge, how to restructure memory content into the brain entry format, what confidence to assign (typically `confidence_source: imported`), whether to merge multiple related memories into a single brain entry.

### /distill-learn {topic}

**Goal:** Active learning facilitation — verify the user's understanding of a new concept through Socratic questioning, cross-reference with existing brain entries for transfer testing, and only distill once comprehension is demonstrated.

**Must do:**
1. Read root INDEX.md and relevant domain indexes to know what the user already has in their brain
2. Ask the user to explain the concept in their own words — do NOT lecture or teach first
3. Probe for genuine understanding: ask "why" and "how" questions, not "what" questions. Target the mechanism, not the definition.
4. Test transfer: find existing brain entries that share structural similarities with the new concept and ask the user to identify the connection. ("Your brain has X — how does this new concept relate?")
5. Identify and challenge weak spots: if the user's explanation is surface-level or contains misconceptions, ask follow-up questions that expose the gap — don't correct directly
6. Once the user demonstrates understanding (can explain mechanism, can transfer to other contexts, can identify edge cases), proceed to `/distill` the verified insight
7. The resulting entry gets `confidence_source: verified` and higher confidence than a standard extraction

**Brain-based challenging:** Don't only test whether the user understands the new concept — use existing brain entries to create harder challenges:
- Find entries that structurally resemble the new concept and ask the user to identify the connection
- Find entries that might CONTRADICT the new concept and ask the user to reconcile
- Identify what's MISSING from the brain that this new concept implies ("Your brain has nothing about X, but this concept depends on it — what do you know about X?")

**Your call:** How many questions to ask (adapt to complexity — simple concept may need 2, deep concept may need 5+), which existing brain entries are structurally similar enough for transfer testing, when to push harder vs when the user has demonstrated sufficient understanding, whether to suggest the user revisit the topic later if comprehension is clearly insufficient.

### /distill-challenge {idea or topic}

**Goal:** Pressure-test an idea, decision, or assumption through three layers of challenge — using the brain as targeting context but drawing ammunition from outside the user's knowledge.

**Must do:**
1. Read root INDEX.md and relevant domain entries to understand the user's existing knowledge landscape
2. Ask the user to state their idea or position clearly
3. Apply three layers of challenge, one at a time:

   **Layer 1 — Internal contradictions:** Find entries in the brain that contradict or tension with the stated idea. Ask the user to reconcile. ("Your brain says X, but now you're saying Y. What changed, or what am I missing?")

   **Layer 2 — Blind spot attack:** Analyze what's ABSENT from the brain that the idea depends on. Challenge the gaps. ("Your brain has 5 entries on AI engineering but 0 on user research. Your idea assumes users want this — what evidence do you have?")

   **Layer 3 — Outside perspective:** Draw from LLM training knowledge, web search if needed, or cross-domain analogies the user has NOT encountered. Introduce genuinely new information that challenges the idea's premises. ("Research in cognitive science suggests the opposite of what you're assuming. How do you respond?")

4. After each layer, let the user respond. Adapt: if they have a good answer, push harder. If they're struggling, help them articulate why.
5. Do NOT be sycophantic. Never say "that's interesting" — take a position and state what evidence would change your mind.
6. End with a synthesis: what survived the challenge, what didn't, what needs more evidence.
7. If the idea survives all three layers, offer to `/distill` the stress-tested insight with `confidence_source: challenged`.

**Anti-sycophancy rules:**
- Never say "that's an interesting approach" — take a position instead
- Never say "there are many ways to think about this" — pick one and state what evidence would change your mind
- Never say "that could work" — say whether it WILL work based on available evidence, and what's missing
- Challenge the strongest version of the user's claim, not a strawman

**Your call:** How hard to push (adapt to user's engagement — if they're energized by challenge, go harder; if they're frustrated, offer a constructive reframe), whether to use web search for Layer 3, how many rounds per layer, when to stop and synthesize.

### /distill-repo {url or local path}

**Goal:** Analyze a codebase to extract architecture patterns and design decisions, guide the user to understand them through Socratic questioning, and store both the architecture reference and verified insights.

**Must do:**
1. Read the repo progressively:
   - Layer 0: file tree + README → understand what it is
   - Layer 1: entry points + architecture docs → understand structure
   - Layer 2: **AT LEAST 3** specific files that represent core design decisions → deep dive into actual implementation
   - **You MUST complete Layer 2 before presenting anything to the user. "As needed" is not an option — Layer 2 is always needed.**
2. Present architecture overview **with code-level evidence**:
   - For each design decision, cite specific file and describe the relevant code pattern
   - Present at least 3 concrete design decisions with evidence, not abstract descriptions
   - **If you cannot cite code evidence for a claim, you haven't read enough. Go back to Layer 2.**
3. **Quality gate before Socratic mode — must pass BOTH:**
   - **Gate A (hard):** For each design decision you presented, can you explain WHY the author made that choice (not just WHAT)? If no → read more code. Do NOT proceed.
   - **Gate B (thinking framework):** Read the user's brain INDEX, then answer these three questions internally before proceeding:
     - What in this repo is **the same** as patterns already in the user's brain?
     - What is **different** from what the user already knows?
     - What is **worth learning** — new knowledge that would change the user's behavior in future projects?
   - If you cannot identify at least one item for each question, you haven't understood the repo deeply enough. Go back to Layer 2.
   - **Red flags that you're rushing:** your overview uses only information from README, you describe file structure without code content, you say "likely" or "probably" about design choices you could verify by reading the code.
4. Switch to Socratic mode: use your Gate B answers to drive the conversation — ask the user about the differences and new patterns you found, test transfer against existing brain entries ("Your brain has Z — how does this repo's approach compare?")
5. Store two types of entries after user confirmation:
   - `type: reference` — architecture skeleton with key design decisions and rationale. Always store this for repos, since code may not be in training data. `source: repo:{url}`
   - `type: insight/pattern` — user's verified understanding and cross-repo patterns. `confidence_source: verified`
6. Run `/distill-compile` to update indexes

**Your call:** Which files are architecturally significant, how many Socratic questions to ask, what relationships to infer with existing brain entries, whether to suggest the user clone and explore the repo hands-on for deeper understanding.
