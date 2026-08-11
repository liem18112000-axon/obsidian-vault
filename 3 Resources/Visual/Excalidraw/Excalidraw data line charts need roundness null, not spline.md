---
ai_hash: 20bde252a2d7a45b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-20
entities: []
source: session 2026-06-20 LUZ-154613
status: seedling
tags:
- excalidraw
- charts
- gotcha
- dataviz
title: Excalidraw data line charts need roundness null, not spline
type: lesson
---

# Excalidraw data line charts need roundness null, not spline

On a quantitative Excalidraw line chart (latency-vs-K, CPU-vs-size, any benchmark plot), set the data polyline's `roundness` to `null` — straight segments between points. Do **not** use `roundness: {"type": 2}` (Catmull-Rom spline): it overshoots between data points and renders values that were never measured.

**Why it matters:** the spline curves *past* each vertex, so a peak can render above the axis maximum, and a single anomalous low point renders as a smooth rounded valley — which visually implies the neighbouring points were also low and hides that it was one outlier. A reader can't tell invented curve from real data.

**When spline is fine:** decorative/flow diagrams, arrows, organic shapes — anywhere the line is illustrative, not quantitative.

Hit while building the LUZ-154613 count-fanout CPU/mem chart: the 480k single-`kubectl top`-snapshot CPU dip (one bad sample) rendered as a fake smooth trough until I switched the line to `roundness: null`, which made it a sharp V that correctly reads as an outlier.

See [[excalidraw-diagram skill]].

## Related

- [[excalidraw-diagram skill]]

%% ai-graph-start %%

**Related notes:**
- _(none above threshold)_

%% ai-graph-end %%