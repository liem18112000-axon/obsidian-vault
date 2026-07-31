---
title: "Excalidraw render script does not auto-wrap bound text"
created: 2026-06-20
type: lesson
status: seedling
source: "session 2026-06-20 appsflyer-data-connector docs"
tags: [excalidraw, diagrams, gotcha, rendering]
---

# Excalidraw render script does not auto-wrap bound text

The Playwright-based render script in the excalidraw-diagram skill (`references/render_excalidraw.py`) does **not** auto-wrap text bound to a container the way the interactive Excalidraw editor does. A long bound label renders on one line and gets clipped at the box edge.

**Fix:** manually insert `\n` into bound (container) text to wrap it to the box width — treat it exactly like free-floating text, which the skill already warns never wraps. So under this renderer, *no* text wraps automatically; wrap everything by hand.

**Sizing rule of thumb:** chars-per-line ≈ `floor(boxWidthPx / (fontSize × 0.6))` for `fontFamily: 3` (the code font). Size the box height to the number of wrapped lines (`lines × fontSize × lineHeight + padding`).

Discovered while building the AppsFlyer connector docs diagrams (`assets/src/*.excalidraw`): the ga4 box paragraph and several `common/` module labels overflowed until each was hand-wrapped and the over-long single-line module rows were shortened.

Render to verify: `uv run python render_excalidraw.py file.excalidraw --output out.png` then Read the PNG — clipping only shows in the render, never in the JSON.
