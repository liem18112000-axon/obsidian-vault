---
title: "Center text in Excalidraw boxes by manual y-positioning, not verticalAlign"
created: 2026-06-18
updated: 2026-06-18
type: lesson
status: seedling
source: "session 2026-06-18"
tags: [excalidraw, diagrams, text-alignment, gotcha, rendering]
---

# Center text in Excalidraw boxes by manual y-positioning, not verticalAlign

To vertically center text inside a box with the `render_excalidraw.py` renderer (the `excalidraw-diagram` skill), **do not rely on `verticalAlign: "middle"`** — neither on standalone text nor on container-bound text.

- **Standalone text** (`containerId: null`) is drawn at its own `y`; a tall `height` + `verticalAlign:middle` does nothing — it sits at the top.
- **Bound text** (`containerId` + container `boundElements`) also rendered **top-anchored** here, not centered. (Binding does NOT reliably fix vertical centering in this renderer — an earlier assumption that it would was wrong.)
- `textAlign: "center"` for the **horizontal** axis *does* work on standalone text — set the text width to the box width and it centers left-right.

## Fix: compute y from the real text height
Place the text block dead-center by measuring it yourself — `lines × fontSize × lineHeight` — and offsetting:
```js
const LH = 1.35;                          // same value you set on the text's lineHeight
const th = text.split('\n').length * fontSize * LH;
push(rect(x, y, w, h, bg, border));
push(txt(x + pad, y + (h - th) / 2,       // <- vertical center
         w - 2*pad, th, text, fontSize, fg, { lh: LH, align: 'center' })); // <- horizontal center
```
Works for rectangles and ellipses alike (e.g. a single-char number badge centered in a circle: `th = 1*fontSize*LH`). Standalone text only — no binding needed.

Found while generating the Claude Hooks & Skills deck diagrams in `C:\Users\dvtliem\.claude\docs\hook-present\build` (gen-05.js / gen-loops.js): box labels first authored as standalone text, then as bound text, both rendered top-aligned; manual y-centering is what actually fixed it.

## Sibling gotcha: standalone text does not auto-wrap either
Same root cause — a standalone text element ignores its `width` for wrapping; a long string overflows past the box (and balloons the export bounding box) instead of wrapping. Fix: pre-wrap the string with explicit `\n` at a max char count (~`boxWidthPx / (fontSize*0.6)`), e.g.:
```js
const wrap = (s, max=40) => { const o=[]; let l=''; for (const w of s.split(' ')) {
  if ((l+' '+w).trim().length>max){o.push(l.trim());l=w;} else l=(l+' '+w).trim(); }
  if(l)o.push(l.trim()); return o.join('\n'); };
```
Then size the containing card to the wrapped line count so nothing clips.

## Related
- [[QA a pptx on Windows: LibreOffice to PDF then PyMuPDF render (thumbnail.py AF_UNIX fails)]]
- [[pptxgenjs addImage stretches when w/h aspect drifts from the real image — read PNG IHDR size]]
