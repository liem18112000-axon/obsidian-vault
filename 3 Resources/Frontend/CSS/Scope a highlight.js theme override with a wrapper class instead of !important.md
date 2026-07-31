---
title: "Scope a highlight.js theme override with a wrapper class instead of !important"
created: 2026-07-11
type: technique
status: seedling
source: "vinnstack session 2026-07-11: IntelliJ-themed Gherkin block"
tags: [css, highlight.js, specificity, syntax-highlighting, theming]
---

# Scope a highlight.js theme override with a wrapper class instead of !important

When a page has one shared highlight.js theme (a set of global `.hljs-*` color rules) but one specific code block needs its own distinct palette, wrap that block's `<pre>` in an extra scoping class — e.g. `gherkin-idea` — and write the override as `.gherkin-idea .hljs-keyword { color: ... }` instead of editing the shared rule or reaching for `!important`.

Why this works: a two-class-deep selector (`.gherkin-idea .hljs-keyword`) has higher CSS specificity than the shared single-class rule (`.hljs-keyword`), so it wins regardless of source order — no `!important` needed. And because CSS cascades per property, not per rule block, any property the scoped override does *not* redeclare (e.g. `font-style: italic` on `.hljs-comment`, `font-weight: 700` on `.hljs-strong`) still falls through from the shared rule. So the scoped override only needs to restate the properties that actually differ (usually just `color`), and everything else is inherited "for free."

This lets multiple highlight.js "skins" coexist on the same page, each scoped to a different component, while still sharing one global light/dark CSS-variable pattern (`:root` / `html.dark` blocks) for anything common to all of them.

Applied in vinnstack: `components/ui/GherkinCode.tsx` needed an IntelliJ/Darcula-inspired palette for BDD scenario blocks, distinct from the generic github-light/dark theme `Markdown.tsx` uses elsewhere on the same page — solved by adding a `gherkin-idea` class to its `<pre>` and scoping the new token colors under it in `app/globals.css`, leaving the shared `.hljs-*` rules untouched.
