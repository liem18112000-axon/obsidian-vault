---
ai_hash: 769226ed90907e55
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: leo-cdp-framework flow.png re-export 2026-06-06
status: seedling
tags:
- excalidraw
- diagrams
- playwright
- cli
- howto
title: Render .excalidraw to PNG headlessly with excalidraw-brute-export-cli
type: howto
---

# Render .excalidraw to PNG headlessly with excalidraw-brute-export-cli

To regenerate an Excalidraw PNG from a `.excalidraw` source without opening the editor (e.g. after editing the JSON directly, or in CI), use **`excalidraw-brute-export-cli`** — it drives a headless browser running the real Excalidraw, so the hand-drawn style + fonts match the GUI export.

```bash
npx -y excalidraw-brute-export-cli -i flow.excalidraw -o flow.png -f png -b true -s 2 --headless true
```

Gotchas hit in practice:
- **`-s | --scale` is REQUIRED** (no default) — it errors out without it. `-s 2` ≈ retina; the resulting pixel size = scene size × scale.
- **It uses Firefox, not Chromium** (the code intercepts requests in a way Chromium blocks), so you must have Playwrights Firefox browser installed — **at the exact Playwright version the CLI bundles**, or you get `Executable doesnt exist at .../firefox-<rev>`. Install via the bundled Playwright, not a global one:
  ```bash
  node "<npx-cache>/_npx/<hash>/node_modules/playwright/cli.js" install firefox
  ```
  (CLI v0.4.0 bundles Playwright 1.60.0 → firefox-1522.)
- On Git Bash/MSYS, prefix with `MSYS_NO_PATHCONV=1` so `-i`/`-o` paths arent mangled.
- `-b true` keeps the background (omit/false → transparent, RGBA).

Note: the rendered PNG is a build artifact of the `.excalidraw` source — editing the JSON text fields (e.g. via Edit) updates the source, but the committed PNG stays stale until re-exported like this.

## Related
- [[Same-repo branch push fires both push and pull_request events (duplicate CI runs)]]

%% ai-graph-start %%

**Related notes:**
- [[Render Excalidraw-style hand-drawn PNGs headlessly with rough.js in the Playwright browser]]
- [[Excalimate export is browser-only; headless export needs Playwright + share URL]]
- [[Export a static .excalidraw from an Excalimate animated scene via get_scene]]
- [[Rasterize SVG to PNG offline with Node sharp (and Excalidraw via hand-SVG)]]
- [[Excalidraw text does not auto-wrap or auto-center]]

%% ai-graph-end %%