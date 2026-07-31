---
ai_hash: 351b4e0e6cbc3a35
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack session 2026-07-08
tags:
- css
- tailwind
- dark-mode
- design-pattern
title: Theme shared overlays with CSS-variable-backed Tailwind classes, not hardcoded
  colors
type: lesson
---

# Theme shared overlays with CSS-variable-backed Tailwind classes, not hardcoded colors

A shared overlay/modal component (lightbox, popover, toast, etc.) that appears on top of an app supporting both light and dark themes should use theme-aware classes tied to CSS custom properties (e.g. Tailwind classes like `bg-paper`/`bg-panel`/`border-line` backed by `--bg-rgb`/`--panel-rgb`/`--line-rgb` variables that get redefined under an `html.dark` selector) — never a hardcoded literal like `bg-white`.

A hardcoded light-mode color on a shared overlay will "work" by coincidence in light mode (blending reasonably with an already-light page), but reads as visibly broken/jarring in dark mode — a stark white box appearing out of nowhere against a dark screen. The bug is invisible unless someone actually tests the dark-mode path.

Concretely: reuse the exact same background/border classes the *inline* version of the content already uses, so the enlarged/modal view is visually just "a bigger version of the same card" in both themes, rather than a separately-styled (and easily out-of-sync) element.

%% ai-graph-start %%

**Related notes:**
- [[Inline SVG ignores theme unless shapes use CSS-variable classes, not hardcoded hex]]
- [[Theme toggle that overrides prefers-color-scheme via data-theme on root]]
- [[Scope a highlight.js theme override with a wrapper class instead of !important]]
- [[Inline style width beats Tailwind breakpoint width classes]]
- [[HTML report theme toggle reload gotcha]]

%% ai-graph-end %%