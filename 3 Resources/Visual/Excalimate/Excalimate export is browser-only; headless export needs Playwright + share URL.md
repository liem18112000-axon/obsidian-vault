---
ai_hash: 02073f3501066b8b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: seedling
tags:
- excalimate
- export
- playwright
- mcp
- gif
- mp4
title: Excalimate export is browser-only; headless export needs Playwright + share
  URL
type: howto
---

# Excalimate export is browser-only; headless export needs Playwright + share URL

Excalimate has **no server-side or MCP render path** — the only renderer is the browser web app (MP4/WebM via WebCodecs, GIF via gif.js, animated SVG). The 23 MCP tools build/animate/query/checkpoint/share a scene but cannot produce a video/GIF file. So any "export to a file" must drive the app: `share_project()` → self-contained E2E-encrypted URL → headless browser (Playwright) loads it → run the in-app export → capture the blob download to a destination path.

Verified export-dialog selectors on app.excalimate.com (no data-testid/aria-label; select by visible text):
- open: button "Export"; then tab "Video" (Image tab is default)
- format select: button whose accessible name STARTS with `MP4`/`WebM`/`GIF`/`SVG`/`Lottie`/`dotLottie` — anchor with regex `/^MP4\b/` so it does not also match the action button "Export MP4"
- quality: buttons "Low"/"Medium"/"High"/"Very High" (plain buttons, no checked state)
- output theme: radios "Light"/"Dark"
- start: button "Export MP4" / "Export GIF" / ...
- delivery: blob download named `excalimate-export.<ext>` (anchor with download attr) — capture via Playwright `download.saveAs(dest)`, or hook `HTMLAnchorElement.prototype.click` to grab the blob as base64.

Implemented as the `export-optimization` skill: `references/export-to-file.mjs` + `references/programmatic-export.md`.

See [[3 Resources/Visual/Excalimate/Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]].

## Related

- [[3 Resources/Visual/Excalimate/Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]]

%% ai-graph-start %%

**Related notes:**
- [[Excalimate cloud share links are CORS-broken — use Connect to MCP server instead]]
- [[Excalimate is AI skills plus an optional MCP server, not one app]]
- [[Render .excalidraw to PNG headlessly with excalidraw-brute-export-cli]]
- [[Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]]
- [[Export a static .excalidraw from an Excalimate animated scene via get_scene]]

%% ai-graph-end %%