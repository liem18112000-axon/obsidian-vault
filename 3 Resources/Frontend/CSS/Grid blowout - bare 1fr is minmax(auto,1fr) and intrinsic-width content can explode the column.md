---
ai_hash: 324b0df5e3caba40
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23
status: seedling
tags:
- css
- grid
- layout
- gotcha
- vinnstack
title: Grid blowout - bare 1fr is minmax(auto,1fr) and intrinsic-width content can
  explode the column
type: lesson
---

# Grid blowout - bare 1fr is minmax(auto,1fr) and intrinsic-width content can explode the column

CSS grid's bare `1fr` track is `minmax(auto, 1fr)` — the AUTO MINIMUM means the track can never shrink below its content's intrinsic width. A wide intrinsic child (natural-size SVG/mermaid diagram, unbroken string, wide table) silently expands the whole column past the viewport ('grid blowout'), pushing siblings off the right edge. The layout can look fine for months if a `max-w-*` cap keeps intrinsic width small — then break the moment the cap is lifted (Vinnstack: the wide-content toggle exposed it; a ~3000px mermaid SVG blew the main column to 3276px in a 1604px slot).

Fix: `minmax(0, 1fr)` (Tailwind: `grid-cols-[300px_minmax(0,1fr)]`) — the flexbox equivalent of putting `min-w-0` on a flex child. Rule of thumb: any grid column that will contain user/AI-generated content (diagrams, code, tables) should be `minmax(0,1fr)`, never bare `1fr`.

Diagnostic signature: an ancestor with `scrollWidth >> clientWidth` whose own overflow-x is visible, while every inner scroll container looks correct.

## Related

- [[Toggle a layout mode with one Tailwind descendant override instead of threading state]]

%% ai-graph-start %%

**Related notes:**
- [[Debug UI overflow by headless reproduction with DOM overflow diagnostics, not blind CSS guesses]]
- [[Inline style width beats Tailwind breakpoint width classes]]
- [[Gridflex items default to min-height auto, blocking inner overflow scroll; add min-h-0]]
- [[Stack a rail-and-content row responsively with flex-col to lgflex-row]]
- [[Toggle a layout mode with one Tailwind descendant override instead of threading state]]

%% ai-graph-end %%