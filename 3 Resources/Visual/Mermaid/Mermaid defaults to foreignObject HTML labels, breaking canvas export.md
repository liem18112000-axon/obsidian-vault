---
ai_hash: 031c654eb98557a0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack session 2026-07-08
status: seedling
tags:
- mermaid
- svg
- canvas
- gotcha
title: Mermaid defaults to foreignObject HTML labels, breaking canvas export
type: lesson
---

# Mermaid defaults to foreignObject HTML labels, breaking canvas export

Since Mermaid v10, node and edge labels render as HTML wrapped in an SVG `<foreignObject>` by default (`htmlLabels` defaults to `true`) — not as plain SVG `<text>`.

This is invisible on screen (the diagram just looks like normal rendered text), but it means the generated SVG always contains `foreignObject`, so [[SVG foreignObject taints a canvas on drawImage]] applies to it: any "export this diagram as a PNG via canvas" feature will silently fail (canvas permanently tainted) the moment the diagram has any text label — i.e. on virtually every real diagram.

The fix is to disable `htmlLabels` in Mermaid's init config, which falls back to plain SVG `<text>` labels (slightly different text-wrapping behavior, but functionally equivalent and canvas-export-safe). See [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]] for the exact config key that actually works.

## Related

- [[SVG foreignObject taints a canvas on drawImage]]
- [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]]

%% ai-graph-start %%

**Related notes:**
- [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]]
- [[SVG foreignObject taints a canvas on drawImage]]
- [[Mermaid text clipping causes useMaxWidth shrink, narrow wrappingWidth, and web-font race]]
- [[Mermaid render() leaks its error-bomb SVG into the DOM past a caught throw; fix with suppressErrorRendering]]
- [[Copy an SVG diagram to the clipboard as PNG - viewBox-sized canvas rasterization]]

%% ai-graph-end %%