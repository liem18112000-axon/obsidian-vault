---
ai_hash: 9930af4ed5e035ca
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19 · obsidian-present deck
status: seedling
tags:
- slides
- excalidraw
- pptx
- design
- gotcha
title: Full-bleed slide images need ~16:9 aspect or their text renders too small
type: lesson
---

# Full-bleed slide images need ~16:9 aspect or their text renders too small

A diagram shown as a full-bleed slide image is scaled to fit the slide — and a full-bleed helper that fits by **width** will letterbox any diagram wider than the slide's ~16:9 frame. The wider the source (aspect ratio well above 1.8), the more vertical whitespace it gets and the **smaller its text renders on screen**, even if the text fits its boxes perfectly.

So legibility on a slide depends on the *diagram's aspect ratio*, not just its internal font sizes. Two fixes, in order of preference:
1. Author the diagram near 16:9 (~1.8). A 4-cards-in-a-row PARA diagram at ratio 2.75 had tiny text; making the cards taller + bumping fonts brought it to ~1.82 and the text became large and legible at full-bleed.
2. Or bump the in-diagram font sizes proportionally so they survive the down-scale.

This is distinct from the [[excalidraw text must fit its box]] rule: text can fit its box and still be unreadable once the whole canvas is shrunk to fit a slide. Check by rendering the actual slide (pptx -> PDF -> PNG via fitz) and eyeballing it, not just the raw diagram PNG.

## Related

- [[excalidraw text must fit its box]]

%% ai-graph-start %%

**Related notes:**
- [[Excalidraw text does not auto-wrap or auto-center]]
- [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]]
- [[pptxgenjs addImage stretches when wh aspect drifts from the real image — read PNG IHDR size]]

%% ai-graph-end %%