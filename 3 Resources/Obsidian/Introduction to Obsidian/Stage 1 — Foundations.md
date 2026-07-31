---
ai_hash: 773c19ba9572bbcc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Stage 1
- Foundations
- Roadmap
- Vault
- Folder
- Notes
- Images
- Config
- Obsidian
- Work Vault
- Personal Vault
- .obsidian/ subfolder
- Settings
- Plugins
- Themes
- Content
- .md files
- Markdown
- Standard Markdown
- Markdown Extensions
- Live Preview mode
- Source mode
- Linking Notes
- Links
- Quarkus
- Native image
- Backlink
- Stage 2
- Alias links
- Command Palette
- Ctrl/Cmd + P
- Quick Switcher
- Ctrl/Cmd + O
- Ctrl/Cmd + N
- Ctrl/Cmd + E
- Ctrl/Cmd + click
- New Note
- Edit/Preview Toggle
- New Pane
- Organization
- Folder Hierarchy
- Vault Root
- Inbox Folder
- Structure
- Organizational Philosophy
- Mechanics
---

# Overview

The goal of this stage isn't to build a system — it's to get fluent with the mechanics so the app disappears and you just write. Here's the breakdown.
See [[Roadmap]] for full picture

# The Vault

A vault is simply a folder on your disk. Everything inside it — notes, images, config — belongs to that vault. You can have multiple vaults (e.g. one for work, one personal), and Obsidian treats them as fully isolated worlds.

- Create one via **"Create new vault"** on first launch, pointing at any folder.
- The hidden `.obsidian/` subfolder holds settings, plugins, and themes. Everything else is your content as plain `.md` files.
- Because it's just files, you can back it up, sync it, or edit notes in any other editor (VS Code, vim, whatever).

# Markdown Basics

Obsidian uses standard Markdown with a few extensions. The essentials:

markdown

```markdown
# Heading 1
## Heading 2

**bold**  *italic*  ~~strikethrough~~  `inline code`

- bullet list
1. numbered list
- [ ] task / checkbox

> blockquote

[external link](https://example.com)
```

There's a **Live Preview** mode (renders as you type) and a **Source mode** (raw Markdown). Toggle per-pane; most people stay in Live Preview.

# Creating & Linking Notes

This is the one habit that matters most in week one. Type `[[` and Obsidian autocompletes existing note names — or creates a new note if it doesn't exist yet.

markdown

```markdown
I'm reading about [[Quarkus]] and its [[native image]] tradeoffs.
```

Both become clickable links. The magic: the linked note now "knows" it was referenced (that's a backlink, which you'll explore in Stage 2). You can also alias links with `[[note name|display text]]`.

# Navigation — Learn These Two Shortcuts

These do 80% of the work:

- **Command Palette** — `Ctrl/Cmd + P` — run any command by typing its name. When in doubt, open this.
- **Quick Switcher** — `Ctrl/Cmd + O` — jump to any note by name instantly. This replaces clicking around the file tree.

Worth also knowing: `Ctrl/Cmd + N` (new note), `Ctrl/Cmd + E` (toggle edit/preview), and `Ctrl/Cmd + click` on a link to open in a new pane.

# The One Discipline: Don't Over-Organize

The biggest beginner trap is building an elaborate folder hierarchy on day one. Resist it. In Stage 1 you should:

- Write notes freely, dump them in the vault root or a single `inbox` folder.
- Link liberally with `[[ ]]` whenever one note mentions a concept that deserves its own note.
- Let structure _emerge_ from links rather than imposing it upfront.

You'll decide your real organizational philosophy in Stage 2, once you can feel how links connect things. Premature folders just become cages you fight later.

---

**End-of-stage checkmark:** you should be able to create a note, link it to two others, jump between them with the quick switcher, and find a command via the palette without thinking. That's the whole foundation.

%% ai-graph-start %%

**Related notes:**
- [[Roadmap]]
- [[Stage 3 — Core Plugins]]
- [[Stage 2 — Linking & Structure]]
- [[Stage 4 — Community Plugins]]
- [[Stage 5 — Workflows & Methodology]]

**Relations:**
- Stage 1 — *IS_A* — Foundations
- Stage 1 — *GOAL_IS* — get fluent with mechanics
- Roadmap — *PROVIDES* — full picture
- Vault — *IS_A* — Folder
- Vault — *CONTAINS* — Notes
- Vault — *CONTAINS* — Images
- Vault — *CONTAINS* — Config
- Obsidian — *MANAGES* — Vault
- Obsidian — *TREATS_AS* — isolated worlds
- Vault — *CAN_BE* — Work Vault
- Vault — *CAN_BE* — Personal Vault
- .obsidian/ subfolder — *HOLDS* — Settings
- .obsidian/ subfolder — *HOLDS* — Plugins
- .obsidian/ subfolder — *HOLDS* — Themes
- Content — *IS* — .md files
- Obsidian — *USES* — Markdown
- Markdown — *IS_A* — Standard Markdown
- Standard Markdown — *HAS* — Markdown Extensions
- Live Preview mode — *RENDERS* — as you type
- Source mode — *IS* — raw Markdown
- Linking Notes — *IS_A* — habit
- Obsidian — *AUTOCMPLETES* — Links
- Obsidian — *CREATES* — New Note
- Quarkus — *HAS* — Native image
- Links — *CREATE* — Backlink
- Backlink — *EXPLORED_IN* — Stage 2
- Alias links — *USES_FORMAT* — note name|display text
- Command Palette — *ACTIVATED_BY* — Ctrl/Cmd + P
- Quick Switcher — *ACTIVATED_BY* — Ctrl/Cmd + O
- Ctrl/Cmd + N — *CREATES* — New Note
- Ctrl/Cmd + E — *TOGGLES* — Edit/Preview Toggle
- Ctrl/Cmd + click — *OPENS* — New Pane
- Organization — *AVOIDS* — Folder Hierarchy
- Notes — *CAN_BE_IN* — Vault Root
- Notes — *CAN_BE_IN* — Inbox Folder
- Structure — *EMERGES_FROM* — Links
- Organizational Philosophy — *DECIDED_IN* — Stage 2
- Stage 1 — *INVOLVES* — writing notes freely
- Stage 1 — *INVOLVES* — linking liberally
- Stage 1 — *INVOLVES* — letting structure emerge
- Stage 1 — *FOUNDATION_INCLUDES* — create a note
- Stage 1 — *FOUNDATION_INCLUDES* — link it to two others
- Stage 1 — *FOUNDATION_INCLUDES* — jump between them with quick switcher
- Stage 1 — *FOUNDATION_INCLUDES* — find a command via palette

%% ai-graph-end %%