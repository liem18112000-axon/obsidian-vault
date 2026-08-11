---
ai_hash: 06a9e4349e67cc9a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities: []
source: session 2026-07-20
status: seedling
tags:
- css
- print
- pdf
- headless-chrome
- gotcha
title: PDF page count cannot prove a fixed-height page fits when overflow is hidden
type: lesson
---

# PDF page count cannot prove a fixed-height page fits when overflow is hidden

When forcing a print layout onto one page with a fixed-height container (`height:297mm`), do NOT add `overflow:hidden` — Chromium will clip any overflowing content silently and the rendered PDF still reports 1 page, so a page-count check passes while the bottom of the document is missing (happened: last 5 CV skill lines vanished). Leave overflow visible so excess content spills onto page 2, making "does not fit" machine-detectable via page count; then shrink type/margins until the count drops to 1, and confirm with a visual render that the last line is present. Related layout fit levers, in order of yield: put dates inline right-aligned on the role line instead of on their own line (one line saved per entry), then font-size/line-height, then per-section margins.

## Related

- [[Update a designed PDF without its source by rebuilding as HTML and printing with headless Edge]]
- [[Absolutely-positioned list bullets slide under a floated sidebar]]

%% ai-graph-start %%

**Related notes:**
- [[Absolutely-positioned list bullets slide under a floated sidebar]]
- [[Update a designed PDF without its source by rebuilding as HTML and printing with headless Edge]]
- [[Debug UI overflow by headless reproduction with DOM overflow diagnostics, not blind CSS guesses]]

%% ai-graph-end %%