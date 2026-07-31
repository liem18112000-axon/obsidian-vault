---
ai_hash: a26596c05643070a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack Mermaid toolbar
status: seedling
tags:
- svg
- canvas
- clipboard
- mermaid
title: Copy an SVG diagram to the clipboard as PNG - viewBox-sized canvas rasterization
type: howto
---

# Copy an SVG diagram to the clipboard as PNG - viewBox-sized canvas rasterization

Pipeline to put a rendered SVG (e.g. a Mermaid diagram) on the clipboard as a PNG:

1. Parse the SVG string (`DOMParser`), read `viewBox` for intrinsic w/h, and SET EXPLICIT `width`/`height` attributes - canvas `drawImage` cannot rasterize an SVG whose width is `100%`/absent (draws 0x0 or throws taint-free garbage).
2. Serialize -> `Blob {type: image/svg+xml}` -> `URL.createObjectURL` -> load into `new Image()` (object URLs keep the canvas untainted, unlike cross-origin images).
3. Draw onto a canvas at 2x scale for crispness; fill white first (diagrams assume a light background; PNG would otherwise be transparent and unreadable pasted on dark surfaces).
4. `canvas.toBlob(png)` -> `navigator.clipboard.write([new ClipboardItem({"image/png": blob})])`. Chromium-only reliably; fall back to downloading the blob when ClipboardItem is missing.

Caveats: CSS custom properties in the SVG (font-family: var(...)) don't resolve inside the rasterized image - text falls back to generic fonts; `URL.revokeObjectURL` in a finally.

Same root cause, different symptom: injecting a `width="100%"`-only SVG into a fullscreen overlay/lightbox renders it at **0x0** (an invisible "blank" overlay) - `width:auto` CSS inside a shrink-to-fit container has no intrinsic size to resolve against, even though the identical markup renders fine in normal block flow. The explicit-pixel-size-from-viewBox fix serves both consumers; also strip mermaid's inline `style="max-width: Npx"` so the consumer's own max-w/max-h constraints govern.

%% ai-graph-start %%

**Related notes:**
- [[SVG foreignObject taints a canvas on drawImage]]
- [[Mermaid text clipping causes useMaxWidth shrink, narrow wrappingWidth, and web-font race]]
- [[Rasterize SVG to PNG offline with Node sharp (and Excalidraw via hand-SVG)]]
- [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]
- [[Render Excalidraw-style hand-drawn PNGs headlessly with rough.js in the Playwright browser]]

%% ai-graph-end %%