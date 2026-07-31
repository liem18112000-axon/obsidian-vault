---
title: "Excalidraw text does not auto-wrap or auto-center"
created: 2026-06-18
updated: 2026-07-31
type: lesson
status: seedling
source: "sessions 2026-06-18 / 2026-06-20 / 2026-07-13"
tags: [excalidraw, diagrams, gotcha, rendering, text-alignment]
---

# Excalidraw text does not auto-wrap or auto-center

Under the `excalidraw-diagram` skill's Playwright renderer (`references/render_excalidraw.py`), **no** text wraps and **no** text vertically centers itself — not free-floating text, and **not** container-bound text either (binding is NOT the fix; the interactive Excalidraw editor wraps bound text, this renderer does not). A long label runs past the box edge and gets clipped; a tall box leaves its text stuck at the top. `verticalAlign: "middle"` is ignored on both. Only `textAlign: "center"` (horizontal) works — set the text width to the box width.

**Wrap by hand.** Insert `\n` yourself at ≈ `floor(boxWidthPx / (fontSize × 0.6))` chars per line (that ratio is for `fontFamily: 3`, the code font), then size the box to the wrapped line count: `height ≥ lines × fontSize × lineHeight + padding`.

```js
const wrap = (s, max=40) => { const o=[]; let l=''; for (const w of s.split(' ')) {
  if ((l+' '+w).trim().length>max){o.push(l.trim());l=w;} else l=(l+' '+w).trim(); }
  if(l)o.push(l.trim()); return o.join('\n'); };
```

**Center by computing y from the real text height** — `lines × fontSize × lineHeight` — and offsetting. Standalone text only; no binding needed. Works for rectangles and ellipses alike (a single-char badge in a circle is just `th = 1*fontSize*LH`):

```js
const LH = 1.35;                          // same value set on the text's lineHeight
const th = text.split('\n').length * fontSize * LH;
push(rect(x, y, w, h, bg, border));
push(txt(x + pad, y + (h - th) / 2,       // vertical center
         w - 2*pad, th, text, fontSize, fg, { lh: LH, align: 'center' })); // horizontal center
```

**Arrows too:** leave an ~8px gap between an arrow's last point and the target shape's edge, or the arrowhead triangle renders inside the box.

Clipping only shows in the raster, never in the JSON — verify with `uv run python render_excalidraw.py file.excalidraw --output out.png` and read the PNG.

Hit in the hook-present deck diagrams (`gen-05.js` / `gen-loops.js`), the AppsFlyer connector docs (`assets/src/*.excalidraw`), and the interview-prep process diagrams for [[nvidia-sdet-interview-prep]].

## Related

- [[QA a pptx on Windows: LibreOffice to PDF then PyMuPDF render (thumbnail.py AF_UNIX fails)]]
- [[pptxgenjs addImage stretches when w/h aspect drifts from the real image — read PNG IHDR size]]
