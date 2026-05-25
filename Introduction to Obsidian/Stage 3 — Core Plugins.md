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