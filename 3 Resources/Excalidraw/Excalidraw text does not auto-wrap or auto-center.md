---
title: "Excalidraw text does not auto-wrap or auto-center"
created: 2026-07-13
type: lesson
status: seedling
source: "session 2026-07-13"
tags: [excalidraw, diagrams, gotcha]
---

# Excalidraw text does not auto-wrap or auto-center

Excalidraw text elements do not auto-wrap to their box width and are not reliably auto-centered vertically by the renderer — a long string runs past the box edge instead of wrapping, and tall boxes leave text stuck at the top.

**Fix:** for every text element (free-floating or bound to a container), manually insert \n at safe line breaks (~ box-width-px / (fontSize*0.6) chars per line), size the box to fit the wrapped text (height >= lines * fontSize * 1.3 + padding), and compute the text's x/y so it's centered in the box rather than relying on verticalAlign:middle to do it.

Also applies to arrows: leave an ~8px gap between the arrow's last point and the target shape's edge, otherwise the arrowhead triangle renders inside the box.

This came up while building interview-prep process diagrams (ADLC lifecycle, BDD pipeline, eArchive architecture) for [[nvidia-sdet-interview-prep]] — the excalidraw-diagram skill's render-and-validate loop is what catches this if skipped.
