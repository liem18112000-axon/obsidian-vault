---
title: "JetBrains Excalidraw plugin rewrites the .excalidraw source field on save"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28"
tags: [excalidraw, tooling, gotcha, diagrams, jetbrains]
---

# JetBrains Excalidraw plugin rewrites the .excalidraw source field on save

When you save a `.excalidraw` file from the **JetBrains Excalidraw plugin**, it silently reformats the JSON on write — notably rewriting the top-level `"source"` field (e.g. from `https://excalidraw.com` to `https://excalidraw-jetbrains-plugin`) and re-indenting the whole file. This shows up as an unexpected "file was modified by a linter" diff right after you write it programmatically. It is harmless: the element content and the render are unaffected, so do not fight it or revert it.

**Rendering `.excalidraw` to an image:** use the `excalidraw-diagram` skill's renderer:
```
cd ~/.claude/skills/excalidraw-diagram/references
uv run python render_excalidraw.py path/to/file.excalidraw            # -> PNG next to the file
uv run python render_excalidraw.py file.excalidraw --format svg       # sharper, ~10x smaller
```
Then Read the produced PNG to visually validate. The script keeps a persistent Chromium profile under `references/.browser-cache/` so repeat renders are faster; pass multiple files in one invocation to skip per-file cold-launch.

**Gotcha:** standalone Excalidraw text does NOT auto-wrap to its element width and is not reliably vertically centered — pre-wrap long strings with explicit `\n` and size boxes to the wrapped line count, or text spills past the box edge.

## Related

- [[Testing Agent builds each pipeline stage as a package mirroring the knowledge_gathering skeleton]]
