---
title: "Mermaid defaults to foreignObject HTML labels, breaking canvas export"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack session 2026-07-08"
tags: [mermaid, svg, canvas, gotcha]
---

# Mermaid defaults to foreignObject HTML labels, breaking canvas export

Since Mermaid v10, node and edge labels render as HTML wrapped in an SVG `<foreignObject>` by default (`htmlLabels` defaults to `true`) — not as plain SVG `<text>`.

This is invisible on screen (the diagram just looks like normal rendered text), but it means the generated SVG always contains `foreignObject`, so [[SVG foreignObject taints a canvas on drawImage]] applies to it: any "export this diagram as a PNG via canvas" feature will silently fail (canvas permanently tainted) the moment the diagram has any text label — i.e. on virtually every real diagram.

The fix is to disable `htmlLabels` in Mermaid's init config, which falls back to plain SVG `<text>` labels (slightly different text-wrapping behavior, but functionally equivalent and canvas-export-safe). See [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]] for the exact config key that actually works.

## Related

- [[SVG foreignObject taints a canvas on drawImage]]
- [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]]
