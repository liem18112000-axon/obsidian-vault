---
title: "Excalimate export is browser-only; headless export needs Playwright + share URL"
created: 2026-06-16
type: howto
status: seedling
source: "session 2026-06-16"
tags: [excalimate, export, playwright, mcp, gif, mp4]
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

See [[Running Excalimate locally: skills in ~/.claude/skills plus MCP server on port 3001]].

## Related

- [[Running Excalimate locally: skills in ~/.claude/skills plus MCP server on port 3001]]
