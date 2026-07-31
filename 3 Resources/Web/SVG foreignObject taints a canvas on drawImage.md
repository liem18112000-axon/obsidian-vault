---
ai_hash: 2b7cd27623cc0029
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack session 2026-07-08
status: seedling
tags:
- svg
- canvas
- browser-security
- gotcha
title: SVG foreignObject taints a canvas on drawImage
type: lesson
---

# SVG foreignObject taints a canvas on drawImage

Drawing an SVG image onto an HTML `<canvas>` via `drawImage()` permanently "taints" that canvas the instant the SVG contains a `<foreignObject>` element — after that, `canvas.toBlob()` and `canvas.toDataURL()` both throw (e.g. `"Tainted canvases may not be exported"`).

This happens even when the SVG is loaded from a same-origin `blob:` URL, which normally would NOT taint a canvas for a plain raster image. Browsers treat `foreignObject` (which lets an SVG embed arbitrary HTML) as a security-sensitive escape hatch, and revoke the canvases export permission unconditionally once any such content is rendered into it, regardless of origin.

There is no in-browser workaround once the SVG has already been drawn — the only fix is to ensure the SVG has no `foreignObject` content *before* rasterizing it (e.g. configure the SVG-generating tool to emit plain `<text>` instead of HTML-in-foreignObject labels).

Found while debugging why [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]] silently broke a "copy diagram as PNG" feature.

## Related

- [[3 Resources/Visual/Mermaid/Mermaid defaults to foreignObject HTML labels, breaking canvas export]]

%% ai-graph-start %%

**Related notes:**
- [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]
- [[Copy an SVG diagram to the clipboard as PNG - viewBox-sized canvas rasterization]]
- [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]]

%% ai-graph-end %%