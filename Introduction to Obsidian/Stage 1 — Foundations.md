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