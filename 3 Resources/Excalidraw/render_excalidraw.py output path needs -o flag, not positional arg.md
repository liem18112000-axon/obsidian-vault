---
title: "render_excalidraw.py output path needs -o flag, not positional arg"
created: 2026-08-26
type: gotcha
status: seedling
source: "session 2026-08-26"
tags: [excalidraw, rendering, cli, gotcha, claude-code]
---

# render_excalidraw.py output path needs -o flag, not positional arg

The excalidraw-to-PNG renderer at `~/.claude/skills/excalidraw-diagram/references/render_excalidraw.py` takes the **output path via the `-o`/`--output` flag**, never as a second positional argument. Every positional arg is parsed as an *input* `.excalidraw` file (the script uses `nargs="+"` for inputs).

**The gotcha:** if you pass the output PNG positionally (`render_excalidraw.py in.excalidraw out.png`), the script treats `out.png` as another input and tries to `read_text(encoding="utf-8")` an existing PNG, failing with:

`UnicodeDecodeError: 'utf-8' codec can't decode byte 0x89 in position 0`

`0x89` is the first byte of the PNG magic number — a reliable tell that a binary image is being read as text.

**Correct invocation** (run from the renderer's uv dir):

```bash
uv run --quiet python render_excalidraw.py input.excalidraw -o output.png -s 2 -w 2400
```

`-s` = device scale (default 2), `-w` = max viewport width (default 1920). Format is inferred from the `-o` extension (or `-f png|svg|both`).

See [[Excalidraw renderer runs from its uv venv references dir]] for the venv/chromium setup.

## Related

- [[Excalidraw renderer runs from its uv venv references dir]]
