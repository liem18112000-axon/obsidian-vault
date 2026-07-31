---
ai_hash: 20b8e9c5f8367106
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-18
entities: []
source: session 2026-06-18
status: seedling
tags:
- pptxgenjs
- pptx
- images
- aspect-ratio
- gotcha
- nodejs
title: pptxgenjs addImage stretches when w/h aspect drifts from the real image — read
  PNG IHDR size
type: lesson
---

# pptxgenjs addImage stretches when w/h aspect drifts from the real image — read PNG IHDR size

When you place an image in pptxgenjs with explicit `w` and `h` (`slide.addImage({path, x, y, w, h})`), pptx scales the image to **exactly** that box. If the box aspect (w/h) does not match the image's true pixel aspect, the image is **stretched/squeezed** — there is no implicit aspect-preserving fit.

A common, sneaky way this breaks: the box w/h is computed from a **hardcoded ratio constant** passed at the call site. When the underlying image is later re-exported at a different size, the constant goes stale and every slide silently squeezes.

## Fix: derive the aspect from the real file, never hand-pass it
Read the PNG's pixel size from its IHDR chunk (width = bytes 16-19, height = 20-23, big-endian) and compute the ratio at build time:
```js
const fs = require('fs');
function pngSize(p){ const b = fs.readFileSync(p); return { w: b.readUInt32BE(16), h: b.readUInt32BE(20) }; }
const { w:pw, h:ph } = pngSize(path); const ratio = pw/ph;
// aspect-fit into box (ax,ay,aw,ah):
let iw = aw, ih = iw/ratio; if (ih > ah) { ih = ah; iw = ih*ratio; }
slide.addImage({ path, x: ax+(aw-iw)/2, y: ay+(ah-ih)/2, w: iw, h: ih });
```

## Related gotchas
- Do NOT pass both explicit `w`/`h` AND `sizing` to `addImage` — that also stretches. Pick one approach (here: manual aspect-fit, no `sizing`).
- For a true full-bleed slide, drop any slide-level heading text and size the box to ~95% of the slide (e.g. `W-0.7` x `H-0.56` on a 13.33x7.5 LAYOUT_WIDE), then aspect-fit + center inside it.

Found while building the Claude Hooks & Skills deck (`C:\Users\dvtliem\.claude\docs\hook-present\build\build-deck.js`): redesigned diagrams changed pixel size, the old per-call ratio args (1.434, 2.680) no longer matched, and slides 9-10 squeezed until the ratio was read from the PNG.

## Related

- [[3 Resources/AI/Claude-Code/pptx/QA a pptx on Windows LibreOffice to PDF then PyMuPDF render (thumbnail.py AF_UNIX fails)]]

%% ai-graph-start %%

**Related notes:**
- [[Full-bleed slide images need ~169 aspect or their text renders too small]]
- [[Excalidraw text does not auto-wrap or auto-center]]
- [[Make one diagram generator double as a reveal-video frame source with STAGE() markers]]
- [[QA a pptx on Windows LibreOffice to PDF then PyMuPDF render (thumbnail.py AF_UNIX fails)]]
- [[LibreOffice headless convert-to leaves soffice.bin locking the source file — silent stale writes]]

%% ai-graph-end %%