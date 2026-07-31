---
ai_hash: 840092da8430e006
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Stage 3 — Core Plugins
- Stage 1 — Foundations
- Stage 2 — Linking & Structure
- Obsidian
- Core plugins
- Settings → Core plugins
- Community ecosystem
- Stage 4 — Community Plugins
- Daily Notes plugin
- Templates plugin
- Backlinks plugin
- Outgoing Links plugin
- Tag Pane plugin
- Search feature
- Embeds syntax
- Callouts syntax
- Templater plugin
- Dataview plugin
- Tasks plugin
- Klara
- Luz
- Daily-note template
- Date format YYYY-MM-DD
- Folder /daily
- Dynamic placeholders
- Search operators
- Note content embedding
- Image embedding
- Styled blocks
- Community plugins
- timestamped note
- daily log
- Markdown block
- panes
- links to current note
- unlinked mentions
- links from current note
- tags
- nested-tag scheme
- gotchas
- technical notes
- MOC
- Settings → Daily notes
- scripting and prompts
- daily note
---

# Overview
[[Stage 1 — Foundations]] and [[Stage 2 — Linking & Structure]] were concepts you applied by hand. Stage 3 is where Obsidian starts doing work _for_ you. Core plugins ship with the app — no downloads, no risk — and you toggle them under **Settings → Core plugins**. Master these before touching the community ecosystem in Stage 4, because half the community plugins just extend these.

# Daily Notes — The Capture Engine

This creates a timestamped note for today with one click (or keyboard shortcut). It's the backbone of almost every Obsidian workflow.

- Configure under **Settings → Daily notes**: set a date format (`YYYY-MM-DD` is standard), a folder (`/daily`), and an optional template.
- Use it as your **inbox**: dump meeting notes, standup items, random thoughts, links to chase later. You sort/link them properly afterward.
- Because each day is `[[2026-05-25]]`, you get a natural timeline you can link to — "decided X on `[[2026-05-25]]`" creates a permanent, navigable record.

For your work, this maps cleanly onto a daily log: Klara tickets touched, Luz deploys, decisions made. The backlinks accumulate into an audit trail without effort.

# Templates — Stop Retyping Structure

Lets you insert a predefined block of Markdown into any note. Set a templates folder, write template files, then insert via command palette or hotkey.

A daily-note template example:
```markdown
## Tasks
- [ ] 

## Standup
- Yesterday:
- Today:
- Blockers:

## Notes

## Links touched
```
Core Templates supports a few dynamic placeholders — `{{date}}`, `{{time}}`, `{{title}}`. That's the limit; if you want scripting and prompts, that's **Templater** (community, [[Stage 4 — Community Plugins]]). Start with core Templates first so you understand what you're upgrading from.

# Backlinks & Outgoing Links

You met backlinks in [[Stage 2 — Linking & Structure]] — these core plugins are what render the panes.

- **Backlinks** — shows what links _to_ the current note, plus unlinked mentions.
- **Outgoing Links** — shows what the current note links _out_ to.

Enable both and dock them in the right sidebar. The "unlinked mentions" feature alone is worth it — it surfaces connections you didn't realize you'd made and turns them into real links with one click.

# Tag Pane

Renders all your tags as a browsable, sortable list with counts. Combined with the nested-tag scheme from Stage 2 (`#project/luz`, `#status/active`), this becomes a control panel — click a tag to see every note carrying it. Essential for the "tag adjectives" half of your linking philosophy.

# Search

More powerful than it looks. Beyond plain text, it supports operators:

```
tag:#klara
path:luz/
file:MOC
line:(redis SETNX)
"exact phrase"
/regex/
```

You can combine them: `tag:#project/luz path:notes/` finds Luz-tagged notes only in your notes folder. Saved searches and the ability to embed a query result into a note make this a precursor to Dataview — learn Search well and Dataview's logic will feel obvious later.

# Embeds & Callouts — Built-in, Not Plugins

These aren't toggles but they belong in Stage 3 because they're core syntax you should adopt now.

**Embedding** pulls one note (or part of it) into another:

markdown

```markdown
![[idempotency key design]]          ← whole note
![[idempotency key design#SHA-256]]  ← just that heading's section
![[diagram.png]]                     ← an image
```

This lets a MOC or daily note _show_ content, not just link to it — great for assembling a report from atomic notes.

**Callouts** are styled blocks for emphasis:

```markdown
> [!warning] Pub/Sub limit
> Payloads over 10MB need the Claim-Check pattern.

> [!note] 
> [!tip]
> [!danger]
```

They render as colored, icon'd boxes — useful for flagging gotchas in technical notes so future-you spots them fast.

### Recommended Stage 3 Setup

Enable, in order of payoff: **Daily Notes → Templates → Backlinks → Outgoing Links → Tag Pane → Search**. Then build one daily-note template and use it for a week straight. The habit matters more than the config.

---

**End-of-stage checkmark:** your daily note auto-populates from a template, you capture into it instead of creating scattered notes, you can find anything with a search operator, and you're using callouts to flag the gotchas in your technical notes. At that point the core app is fully under your control — and [[Stage 4 — Community Plugins]] community plugins (Dataview, Templater, Tasks) will feel like natural upgrades rather than magic.

%% ai-graph-start %%

**Related notes:**
- [[Roadmap]]
- [[Stage 4 — Community Plugins]]
- [[Stage 2 — Linking & Structure]]
- [[Stage 1 — Foundations]]
- [[Stage 5 — Workflows & Methodology]]

**Relations:**
- Stage 3 — Core Plugins — *introduces* — Core plugins
- Core plugins — *ship with* — Obsidian
- Core plugins — *are toggled under* — Settings → Core plugins
- Core plugins — *precede* — Community ecosystem
- Community plugins — *extend* — Core plugins
- Community ecosystem — *is part of* — Stage 4 — Community Plugins
- Daily Notes plugin — *is a* — Core plugins
- Daily Notes plugin — *creates* — timestamped note
- Daily Notes plugin — *configured under* — Settings → Daily notes
- Daily Notes plugin — *uses date format* — Date format YYYY-MM-DD
- Daily Notes plugin — *uses folder* — Folder /daily
- Daily Notes plugin — *can use* — Daily-note template
- timestamped note — *forms* — daily log
- daily log — *includes reference to* — Klara
- daily log — *includes reference to* — Luz
- Templates plugin — *is a* — Core plugins
- Templates plugin — *inserts* — Markdown block
- Templates plugin — *supports* — Dynamic placeholders
- Dynamic placeholders — *include* — {{date}} placeholder
- Dynamic placeholders — *include* — {{time}} placeholder
- Dynamic placeholders — *include* — {{title}} placeholder
- Templater plugin — *provides* — scripting and prompts
- Templater plugin — *is a* — Community plugins
- Templater plugin — *is part of* — Stage 4 — Community Plugins
- Templates plugin — *precedes* — Templater plugin
- Backlinks plugin — *is a* — Core plugins
- Outgoing Links plugin — *is a* — Core plugins
- Backlinks plugin — *introduced in* — Stage 2 — Linking & Structure
- Backlinks plugin — *renders* — panes
- Outgoing Links plugin — *renders* — panes
- Backlinks plugin — *shows* — links to current note
- Backlinks plugin — *shows* — unlinked mentions
- Outgoing Links plugin — *shows* — links from current note
- Tag Pane plugin — *is a* — Core plugins
- Tag Pane plugin — *renders* — tags
- Tag Pane plugin — *combines with* — nested-tag scheme
- Search feature — *is a* — Core plugins
- Search feature — *supports* — Search operators
- Search operators — *include* — tag:#klara operator
- Search operators — *include* — path:luz/ operator
- Search operators — *include* — file:MOC operator
- Search operators — *include* — line:(redis SETNX) operator
- Search operators — *include* — "exact phrase" operator
- Search operators — *include* — /regex/ operator
- Search feature — *precedes* — Dataview plugin
- Embeds syntax — *is a* — Core syntax
- Callouts syntax — *is a* — Core syntax
- Embeds syntax — *enables* — Note content embedding
- Embeds syntax — *enables* — Image embedding
- Note content embedding — *can be used in* — MOC
- Note content embedding — *can be used in* — daily note
- Callouts syntax — *creates* — Styled blocks
- Styled blocks — *flag* — gotchas
- gotchas — *are found in* — technical notes
- Daily Notes plugin — *recommended before* — Templates plugin
- Templates plugin — *recommended before* — Backlinks plugin
- Backlinks plugin — *recommended before* — Outgoing Links plugin
- Outgoing Links plugin — *recommended before* — Tag Pane plugin
- Tag Pane plugin — *recommended before* — Search feature
- Dataview plugin — *is a* — Community plugins
- Tasks plugin — *is a* — Community plugins
- MOC — *is a type of* — note
- daily note — *auto-populates from* — Daily-note template
- daily note — *captures* — notes
- Search feature — *finds* — anything
- Callouts syntax — *flags* — gotchas

%% ai-graph-end %%