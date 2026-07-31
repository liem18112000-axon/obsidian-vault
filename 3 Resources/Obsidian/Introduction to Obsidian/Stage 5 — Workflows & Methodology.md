---
ai_hash: 4548953df8ab661c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Stage 5 — Workflows & Methodology
- Stage 1 — Foundations
- Stage 2 — Linking & Structure
- Stage 3 — Core Plugins
- Stage 4 — Community Plugins
- Zettelkasten
- Niklas Luhmann
- Atomicity
- Your own words
- Dense linking
- Emergence
- PARA
- Tiago Forte
- Projects (PARA)
- Areas (PARA)
- Resources (PARA)
- Archives (PARA)
- Luz-Enrichment pipeline
- PIT tax filing
- Klara maintenance
- team mentoring
- Quarkus notes
- GCP patterns
- Shipped projects
- quantum-platform work
- LYT / MOCs
- Nick Milo
- Maps of Content
- Hybrid Method
- Capture-to-Permanent Pipeline
- Capture (Pipeline Step)
- Process (Pipeline Step)
- Connect (Pipeline Step)
- Archive (Pipeline Step)
- Process Cadence
- Daily (Cadence)
- Weekly (Cadence)
- Per-sprint / monthly (Cadence)
- Stage 6 — Advanced Automation
- Ollama
- Claude/MCP
- AI integration
- Plugins
- Methodology
- ML-paper work
- engineering work
- Luz MOC
- daily note
- inbox
- scrum template
- Tasks
- Vault
- Knowledge base
---

# Overview
Here's the truth Stage 5 forces you to confront: **no plugin saves you from a lack of method.** [[Stage 1 — Foundations]] [[Stage 2 — Linking & Structure]] [[Stage 3 — Core Plugins]] [[Stage 4 — Community Plugins]] gave you a powerful machine; 
Stage 5 is deciding what to _do_ with it. Pick wrong and you get a beautiful graph full of notes you never reuse. The methodologies below aren't rules — they're lenses. Most people, you included, will blend two.
# Zettelkasten — Atomic Linked Notes

The original networked-thought method (Niklas Luhmann's "slip-box"). Core principles:
- **Atomicity** — one idea per note. Not "MongoDB" but "Why `allowDiskUse` doesn't bypass the 100MB sort limit per stage." Small enough to link precisely.
- **Your own words** — you rephrase, don't copy. The act of rewriting is the learning.
- **Dense linking** — every note connects to related notes, building a web of _ideas_, not files.
- **Emergence** — structure grows bottom-up from links, not top-down from folders.

**Best for:** research and durable knowledge. This is the method for your ML-paper work — PhoBERT findings, sentiment-analysis techniques, things you'll synthesize across months. Each insight becomes an atomic note that accretes backlinks and eventually _becomes_ a paper section.
**Weakness:** terrible for time-bound, actionable work. A sprint task isn't an atomic idea.
# PARA — Action-Oriented Organization
Tiago Forte's method, organized by _actionability_ rather than topic. Four top-level buckets:

| Bucket        | Definition                          | Your examples                               |
| ------------- | ----------------------------------- | ------------------------------------------- |
| **Projects**  | Active, has a deadline & outcome    | Luz-Enrichment pipeline, PIT tax filing     |
| **Areas**     | Ongoing responsibility, no end      | Klara maintenance, team mentoring           |
| **Resources** | Reference material, future interest | Quarkus notes, GCP patterns                 |
| **Archives**  | Inactive, done                      | Shipped projects, the quantum-platform work |
The genius is the flow: when a project finishes, you _move_ it to Archives. Your active workspace stays uncluttered because only live things sit in Projects and Areas.
**Best for:** your engineering work — it's project-driven with real deadlines and clear "done" states. PARA maps onto how an engineer's week actually works.
**Weakness:** it organizes _files_, not _ideas_. It won't help you connect concepts across projects — that's Zettelkasten's job.
# LYT / MOCs — Linking Your Thinking
You already met this in [[Stage 2 — Linking & Structure]] Nick Milo's approach treats **Maps of Content** as the primary structure: instead of folders or rigid systems, you build hub notes that curate links. It sits comfortably _on top of_ either method above — a `Luz MOC` works whether your underlying philosophy is PARA or Zettelkasten.
**Best for:** the connective tissue. MOCs are how you navigate when search and graph get noisy.
# The Honest Recommendation — A Hybrid
You have two distinct modes, so use two methods:
```
PARA as the skeleton (folders/buckets):
  /1-Projects     ← Luz-Enrichment, current sprint, tax filing
  /2-Areas        ← Klara, team, on-call
  /3-Resources    ← Quarkus, GCP, MongoDB reference notes
  /4-Archive      ← shipped work
  /daily          ← daily scrum notes
  /MOCs           ← hub notes

Zettelkasten as the connective layer:
  - Atomic, linked notes inside Resources (and Archive)
  - This is your research + durable-knowledge web

MOCs as the navigation layer:
  - One per major topic, sitting above both
```
The split mirrors your reality: **Projects/Areas are PARA** (deadline-driven, get archived), while **Resources is a Zettelkasten** (atomic ideas that compound over time). The Quarkus gotcha you learn debugging a Luz service today becomes a Resource note that's still valuable two projects from now — that's the part PARA alone would lose.
# The Capture-to-Permanent Pipeline
Whatever method, the _flow_ between note types is what makes it work:
1. **Capture** → daily note / inbox (fast, messy, no structure — your scrum template lives here)
2. **Process** → periodically, turn captures into proper atomic notes, link them, file under PARA
3. **Connect** → link new notes to existing ones; update the relevant MOC
4. **Archive** → when a project ships, move it; the Resource notes it spawned stay live
Step 2 is the one everyone skips and the one that matters most. A weekly 20-minute "process the inbox" review is the single habit that separates a living vault from a digital junk drawer.
# Pick a Process Cadence
Method needs rhythm. A minimal review schedule:
- **Daily** — capture into the scrum/daily note (already automated from [[Stage 3 — Core Plugins]])
- **Weekly** — process inbox, link new notes, glance at open Tasks
- **Per-sprint / monthly** — archive finished projects, prune orphans (the graph from [[Stage 2 — Linking & Structure]] shows them)
---
**End-of-stage checkmark:** you can say which bucket any new note belongs in without hesitating, your inbox gets processed (not just accumulated), and you've run at least one weekly review. The method is working when the vault feels lighter over time instead of heavier.
That leaves [[Stage 6 — Advanced Automation]] — sync across devices, publishing, and AI integration, which ties back into your Ollama and Claude/MCP work for actually _querying_ this knowledge base.

%% ai-graph-start %%

**Related notes:**
- [[Stage 2 — Linking & Structure]]
- [[Roadmap]]
- [[Stage 3 — Core Plugins]]
- [[Stage 4 — Community Plugins]]
- [[Stage 6 — Advanced Automation]]

**Relations:**
- Stage 5 — Workflows & Methodology — *forces to confront* — no plugin saves you from a lack of method
- Stage 1 — Foundations — *contributed to* — powerful machine
- Stage 2 — Linking & Structure — *contributed to* — powerful machine
- Stage 3 — Core Plugins — *contributed to* — powerful machine
- Stage 4 — Community Plugins — *contributed to* — powerful machine
- Zettelkasten — *is a* — networked-thought method
- Zettelkasten — *developed by* — Niklas Luhmann
- Zettelkasten — *core principle* — Atomicity
- Zettelkasten — *core principle* — Your own words
- Zettelkasten — *core principle* — Dense linking
- Zettelkasten — *core principle* — Emergence
- Zettelkasten — *best for* — ML-paper work
- Zettelkasten — *best for* — research
- Zettelkasten — *best for* — durable knowledge
- Zettelkasten — *weakness* — terrible for time-bound, actionable work
- PARA — *developed by* — Tiago Forte
- PARA — *organized by* — actionability
- PARA — *has bucket* — Projects (PARA)
- PARA — *has bucket* — Areas (PARA)
- PARA — *has bucket* — Resources (PARA)
- PARA — *has bucket* — Archives (PARA)
- Projects (PARA) — *example* — Luz-Enrichment pipeline
- Projects (PARA) — *example* — PIT tax filing
- Areas (PARA) — *example* — Klara maintenance
- Areas (PARA) — *example* — team mentoring
- Resources (PARA) — *example* — Quarkus notes
- Resources (PARA) — *example* — GCP patterns
- Archives (PARA) — *example* — Shipped projects
- Archives (PARA) — *example* — quantum-platform work
- PARA — *best for* — engineering work
- PARA — *weakness* — organizes files, not ideas
- LYT / MOCs — *introduced in* — Stage 2 — Linking & Structure
- LYT / MOCs — *developed by* — Nick Milo
- LYT / MOCs — *treats as primary structure* — Maps of Content
- LYT / MOCs — *sits on top of* — PARA
- LYT / MOCs — *sits on top of* — Zettelkasten
- LYT / MOCs — *best for* — connective tissue
- Hybrid Method — *uses as skeleton* — PARA
- Hybrid Method — *uses as connective layer* — Zettelkasten
- Hybrid Method — *uses as navigation layer* — LYT / MOCs
- PARA — *includes* — Projects (PARA)
- PARA — *includes* — Areas (PARA)
- PARA — *includes* — Resources (PARA)
- PARA — *includes* — Archives (PARA)
- PARA — *includes* — daily note
- PARA — *includes* — Maps of Content
- Zettelkasten — *applies to* — Resources (PARA)
- Zettelkasten — *applies to* — Archives (PARA)
- Maps of Content — *example* — Luz MOC
- Capture-to-Permanent Pipeline — *step* — Capture (Pipeline Step)
- Capture-to-Permanent Pipeline — *step* — Process (Pipeline Step)
- Capture-to-Permanent Pipeline — *step* — Connect (Pipeline Step)
- Capture-to-Permanent Pipeline — *step* — Archive (Pipeline Step)
- Capture (Pipeline Step) — *involves* — daily note
- Capture (Pipeline Step) — *involves* — inbox
- Process (Pipeline Step) — *involves* — turning captures into atomic notes
- Process (Pipeline Step) — *involves* — filing under PARA
- Connect (Pipeline Step) — *involves* — linking new notes to existing ones
- Connect (Pipeline Step) — *involves* — updating relevant MOC
- Archive (Pipeline Step) — *involves* — moving finished projects
- Process Cadence — *includes* — Daily (Cadence)
- Process Cadence — *includes* — Weekly (Cadence)
- Process Cadence — *includes* — Per-sprint / monthly (Cadence)
- Daily (Cadence) — *involves* — capture into scrum/daily note
- Daily (Cadence) — *automated from* — Stage 3 — Core Plugins
- Weekly (Cadence) — *involves* — process inbox
- Weekly (Cadence) — *involves* — link new notes
- Weekly (Cadence) — *involves* — glance at open Tasks
- Per-sprint / monthly (Cadence) — *involves* — archive finished projects
- Per-sprint / monthly (Cadence) — *involves* — prune orphans
- Stage 2 — Linking & Structure — *shows* — orphans
- Stage 6 — Advanced Automation — *includes* — sync across devices
- Stage 6 — Advanced Automation — *includes* — publishing
- Stage 6 — Advanced Automation — *includes* — AI integration
- AI integration — *ties back into* — Ollama
- AI integration — *ties back into* — Claude/MCP
- AI integration — *for* — querying Knowledge base

%% ai-graph-end %%