---
title: "Render .excalidraw to PNG/SVG offline with render_excalidraw.py"
created: 2026-08-19
type: howto
status: seedling
source: "session 2026-08-19"
tags: [excalidraw, diagrams, rendering, tooling]
---

# Render .excalidraw to PNG/SVG offline with render_excalidraw.py

The excalidraw-diagram skill ships a local headless-Chromium renderer, so you can turn a `.excalidraw` JSON file into a PNG or SVG entirely offline — no excalidraw.com round-trip. This is what lets a generated diagram be committed into a repo README as a static image.

```bash
cd ~/.claude/skills/excalidraw-diagram/references
uv run python render_excalidraw.py path/to/diagram.excalidraw --format both   # png | svg | both
```

- Output lands next to the input file (`diagram.png` / `diagram.svg`).
- A persistent Chromium profile in `references/.browser-cache/` makes repeat renders fast (the excalidraw ESM module is served from disk after the first run).
- Pass multiple `.excalidraw` files in one invocation to skip per-file Chromium cold-launch.
- First-time setup: `uv sync` then `uv run playwright install chromium`.

Workflow that uses it: generate the `.excalidraw` (by hand or a small deterministic generator), then run the render → Read the PNG → fix the JSON → re-render loop until clean, then embed the PNG in a README and keep the `.excalidraw`/`.svg` as editable sources.

Related: [[excalidraw-diagram skill]]

## Related

- [[excalidraw-diagram skill]]
