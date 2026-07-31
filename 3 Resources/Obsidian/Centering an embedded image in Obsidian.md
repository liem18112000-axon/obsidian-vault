---
title: Centering an embedded image in Obsidian
created: 2026-06-11
type: howto
status: seedling
source: session 2026-06-11
tags:
  - obsidian
  - markdown
  - formatting
  - gotcha
aliases:
  - Center image Obsidian
  - Obsidian center embed
---

# Centering an embedded image in Obsidian

**To center an embedded image/diagram in Obsidian reading view, wrap the `![[image.png]]` embed in a `<div style="text-align: center">` — but you MUST leave a blank line between the `<div>` tags and the embed, or the wikilink embed renders as literal text instead of an image.**

```markdown
<div style="text-align: center">

![[claude-accesstrade-architecture.png]]

</div>
```

## Why the blank lines matter

Obsidian only re-processes Markdown/Obsidian syntax (like `![[...]]` embeds) *inside* a block-level HTML element when the inner content is separated from the HTML tags by blank lines. Without them, the parser treats the whole `<div>…</div>` as raw HTML and the `![[...]]` is never expanded — you see the literal text `![[image.png]]`.

## Notes & alternatives

- `text-align: center` works because the rendered embed is an inline/inline-block `<img>`.
- Markdown image links `![alt](path)` can be centered the same way.
- This does **not** center Mermaid diagrams — Mermaid blocks render left-aligned and need a CSS snippet in `.obsidian/snippets/` (e.g. `.markdown-rendered .mermaid { text-align: center; } .markdown-rendered .mermaid svg { display: block; margin: 0 auto; }`), which affects the whole vault.
- A cleaner vault-wide option for images is a `cssclass` + snippet, but the inline `<div>` is best for one-off centering.

## Related

- [[Accesstrade API Integration - MOC]]
