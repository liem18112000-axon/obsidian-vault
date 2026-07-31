---
ai_hash: 4bcee3dcf17e86a6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Stage 2
- philosophy
- Stage 1 — Foundations
- mechanics
- Roadmap
- Backlinks
- Links
- Backlinks pane
- idempotency key
- Unlinked mentions
- Tags
- Notes
- Quarkus
- Alvin
- Redis
- Concepts
- Entities
- Ideas
- States
- Types
- Contexts
- Nested Tags
- hierarchy
- Graph View
- Ctrl/Cmd + G
- diagnostic tool
- Orphan notes
- Hubs
- Maps of Content (MOCs)
- scaling problem
- Luz Platform MOC
- luz-docs
- luz-docs-batch
- luz-thumbnail
- Claim-Check pattern
- Aggregator pattern
- idempotency key design
- GKE autoscaling
- Istio DestinationRule tuning
- Luz MOC
- Klara MOC
- Java/Quarkus MOC
- Organizational Philosophy
- Folders
- Hybrid
- metadata
- navigation
- /inbox
- /notes
- /MOCs (folder)
- /daily
- /templates
- /attachments
- Stage 3
- plugins
---

# Overview
[[Stage 1 — Foundations]] was about mechanics. Stage 2 is about _philosophy_ — deciding how your knowledge connects. This is the stage where most people either build a system that scales for years or paint themselves into a corner. Here's the breakdown.
See [[Roadmap]] for full picture
# Backlinks — The Whole Point

A backlink is the reverse of a link. When note A links to note B with `[[B]]`, note B automatically gains a backlink pointing back to A. You don't create them manually — they appear.

- Open the **Backlinks** pane (right sidebar) on any note to see everything that references it.
- This is how knowledge _accretes_: a concept note like `[[idempotency key]]` slowly collects backlinks from every project note that touched it, becoming a hub without you planning it.
- Watch for **"Unlinked mentions"** in the same pane — places where a note's name appears as plain text but isn't yet linked. One click converts them.

# Tags vs. Links — Know the Difference

Beginners conflate these. They serve different purposes:

| |**Links `[[ ]]`**|**Tags `#`**|
|---|---|---|
|Connects|Two specific notes|Many notes by category|
|Direction|Bidirectional|Flat label|
|Best for|Concepts, entities, ideas|States, types, contexts|
|Example|`[[Quarkus]]`, `[[Alvin]]`|`#status/draft`, `#type/meeting`|

Rule of thumb: **link nouns, tag adjectives.** A note _about_ Redis is a link target; a note that is _in-progress_ gets a `#draft` tag. Tags answer "what kind of note is this?"; links answer "what does this note relate to?"

# Nested Tags

Tags support hierarchy with `/`:

```markdown
#project/luz
#status/active
#meeting/standup
```

This keeps the tag pane organized and lets you filter broadly (`#project`) or narrowly (`#project/luz`). Useful for your work context — `#klara`, `#luz/enrichment`, `#tax` and so on.

# The Graph View

`Ctrl/Cmd + G` opens the graph — a visual map of all notes as nodes and links as edges.

It's seductive but mostly a _diagnostic_ tool, not a daily driver. What it's genuinely good for:

- Spotting **orphan notes** (floating with no connections) — these need linking or deleting.
- Seeing which notes are becoming natural **hubs** (dense connection clusters).
- Using filters and groups (color by tag/folder) to sanity-check your structure.

Don't fall into the trap of admiring the graph instead of writing. It's a mirror, not the work.

# Maps of Content (MOCs) — The Key Technique

This is the most important concept in Stage 2. A MOC is a hand-curated hub note that links out to related notes on a topic. Think of it as a manually-built table of contents that you control.

```markdown
# Luz Platform MOC

## Services
- [[luz-docs]]
- [[luz-docs-batch]]
- [[luz-thumbnail]]

## Patterns
- [[Claim-Check pattern]]
- [[Aggregator pattern]]
- [[idempotency key design]]

## Infra
- [[GKE autoscaling]]
- [[Istio DestinationRule tuning]]
```

MOCs solve the scaling problem: the graph and search get noisy as a vault grows, but a MOC is _intentional_ navigation. You'd likely want a `Luz MOC`, a `Klara MOC`, maybe a `Java/Quarkus MOC`. They're notes like any other, so they link, nest, and even reference each other.

# Choosing Your Organizational Philosophy

This is the decision Stage 2 forces. The four camps:

- **Folders** — traditional hierarchy. Familiar but rigid; a note can only live in one place.
- **Tags** — categorical, flat, flexible. Good for status and type.
- **Links** — relational, emergent. The Obsidian-native way.
- **Hybrid** — what almost everyone actually lands on.

The pragmatic consensus: **minimal folders, heavy links, tags for metadata, MOCs for navigation.** Something like:

```
/inbox          ← unsorted captures
/notes          ← the bulk, flat, linked
/MOCs           ← your hub notes
/daily          ← daily notes
/templates      ← (Stage 3)
/attachments    ← images, PDFs
```

That's it. You don't need deep folder trees because links and MOCs do the navigation work — and unlike folders, a note can belong to many contexts at once.

---

**End-of-stage checkmark:** you can explain to someone _why_ you'd link vs. tag a given note, you have at least one MOC built for a real topic (Luz is the obvious candidate), and your graph has no surprise orphans. Once that clicks, you're ready for Stage 3's plugins to automate it.

%% ai-graph-start %%

**Related notes:**
- [[Stage 5 — Workflows & Methodology]]
- [[Stage 3 — Core Plugins]]
- [[Roadmap]]
- [[Stage 1 — Foundations]]
- [[index]]

**Relations:**
- Stage 2 — *focuses on* — philosophy
- Stage 1 — Foundations — *focuses on* — mechanics
- Stage 2 — *references* — Roadmap
- Backlinks — *is reverse of* — Links
- Links — *creates* — Backlinks
- Backlinks pane — *displays* — Backlinks
- Backlinks pane — *displays* — Unlinked mentions
- idempotency key — *collects* — Backlinks
- Links — *contrasts* — Tags
- Links — *connects* — Notes
- Tags — *connects* — Notes
- Links — *has direction* — Bidirectional
- Tags — *has direction* — Flat label
- Links — *best for* — Concepts
- Links — *best for* — Entities
- Links — *best for* — Ideas
- Tags — *best for* — States
- Tags — *best for* — Types
- Tags — *best for* — Contexts
- Links — *has example* — Quarkus
- Links — *has example* — Alvin
- Redis — *is target of* — Links
- Nested Tags — *is a* — Tags
- Nested Tags — *supports* — hierarchy
- Graph View — *is opened by* — Ctrl/Cmd + G
- Graph View — *is a* — diagnostic tool
- Graph View — *helps spot* — Orphan notes
- Graph View — *helps spot* — Hubs
- Maps of Content (MOCs) — *is key technique in* — Stage 2
- Maps of Content (MOCs) — *is a type of* — Hubs
- Maps of Content (MOCs) — *solves* — scaling problem
- Luz Platform MOC — *is an example of* — Maps of Content (MOCs)
- Luz Platform MOC — *links to* — luz-docs
- Luz Platform MOC — *links to* — luz-docs-batch
- Luz Platform MOC — *links to* — luz-thumbnail
- Luz Platform MOC — *links to* — Claim-Check pattern
- Luz Platform MOC — *links to* — Aggregator pattern
- Luz Platform MOC — *links to* — idempotency key design
- Luz Platform MOC — *links to* — GKE autoscaling
- Luz Platform MOC — *links to* — Istio DestinationRule tuning
- Maps of Content (MOCs) — *has example* — Luz MOC
- Maps of Content (MOCs) — *has example* — Klara MOC
- Maps of Content (MOCs) — *has example* — Java/Quarkus MOC
- Stage 2 — *involves* — Organizational Philosophy
- Folders — *is a type of* — Organizational Philosophy
- Tags — *is a type of* — Organizational Philosophy
- Links — *is a type of* — Organizational Philosophy
- Hybrid — *is a type of* — Organizational Philosophy
- Hybrid — *comprises* — minimal folders
- Hybrid — *comprises* — heavy links
- Hybrid — *comprises* — tags for metadata
- Hybrid — *comprises* — MOCs for navigation
- Hybrid — *employs* — /inbox
- Hybrid — *employs* — /notes
- Hybrid — *employs* — /MOCs (folder)
- Hybrid — *employs* — /daily
- Hybrid — *employs* — /templates
- Hybrid — *employs* — /attachments
- Stage 3 — *uses* — plugins
- plugins — *automates* — Stage 2

%% ai-graph-end %%