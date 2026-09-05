---
title: "Excalidraw container-bound text does not auto-wrap in the render script"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17 agent-framework-skeleton diagram"
tags: [excalidraw, diagrams, gotcha, claude-skills]
---

# Excalidraw container-bound text does not auto-wrap in the render script

Excalidraw text elements bound to a container (via `containerId` + the container's `boundElements`) are **not** re-wrapped to the container width by the excalidraw-diagram skill's headless render script. Whatever single line you put in `text`/`originalText` is drawn at its full width and simply overflows past the box edge if it is too long.

**Why it bites:** it looks like normal Excalidraw (where bound text wraps live in the editor), so you assume wrapping and ship a label that clips. Saw it with `AGENT\n(orchestration core)` in a 220px box — "core)" poked out the right side.

**Fix / rule of thumb:**
- Keep in-box labels short, or insert `\n` yourself to hand-wrap.
- Budget ~`floor(innerWidthPx / (fontSize * 0.6))` chars per line for fontFamily 3 (monospace). A 220px box (~188px inner) at fontSize 20 holds ~15 chars/line.
- When unsure, make the box taller and split across lines rather than shrinking the font.

Same failure mode the skill warns about for standalone text — it just also applies to bound text under this renderer.

## Related

- [[excalidraw-diagram skill]]
