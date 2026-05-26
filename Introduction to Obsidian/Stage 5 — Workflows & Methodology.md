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