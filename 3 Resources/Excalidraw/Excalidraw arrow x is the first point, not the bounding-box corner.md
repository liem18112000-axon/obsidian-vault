---
title: "Excalidraw arrow x is the first point, not the bounding-box corner"
created: 2026-08-26
type: gotcha
status: seedling
source: "session 2026-08-26, beforeafterai swimlanes"
tags: [excalidraw, diagram, bounding-box, gotcha, arrows]
---

# Excalidraw arrow x is the first point, not the bounding-box corner

When computing the bounding box of an Excalidraw scene (e.g. to size an enclosing frame), you cannot treat an `arrow`/`line` like a rectangle. For shapes, `x,y` is the top-left corner and `x+width / y+height` is the far corner. For an `arrow`/`line`, `x,y` is the FIRST point (the anchor); `points` are offsets relative to it and `width/height` are just the span magnitudes (always positive). So if the arrow routes leftward or upward, some `points` offsets are negative and the true left/top edge is `x + min(offset)`, which is LESS than `x`. Using `x+width` as the right edge then overshoots badly (it double-counts, giving `x + span` when x is actually the right edge).

Correct bbox per element:
- arrow/line: xs = [x + p[0] for p in points]; ys = [y + p[1] for p in points]; take min/max.
- everything else: x .. x+width, y .. y+height.

Symptom that flagged it: a dotted enclosing frame extended ~1500px into empty canvas because a left-going feedback-loop arrow reported `x=3593, width=2310` -> naive `x+width=5903`, while its real extent was only 1283..3593.

## Related

- [[Testing Agent workflow step to AI Skill mapping]]
