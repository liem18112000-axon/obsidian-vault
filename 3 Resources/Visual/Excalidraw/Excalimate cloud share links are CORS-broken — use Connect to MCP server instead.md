---
title: "Excalimate cloud share links are CORS-broken — use Connect to MCP server instead"
created: 2026-06-16
type: lesson
status: seedling
source: "session 2026-06-16 divide-conquer-count overview"
tags: [excalimate, excalidraw, gif, export, gotcha, cors]
---

# Excalimate cloud share links are CORS-broken — use Connect to MCP server instead

An Excalimate `share_project` link (https://app.excalimate.com/#share=...) can open to a **blank white canvas** because the app's fetch of `https://share.excalimate.com/share/<code>` is blocked by CORS ("No 'Access-Control-Allow-Origin' header") → `Failed to load shared animation`. This is server-side on Excalimate's share host, fails in every browser, and re-sharing does not fix it. The blank is NOT the t=0 reveal state.

Reliable route to view/export a scene built via the Excalimate MCP: open https://app.excalimate.com (no #share hash) → welcome canvas → **Connect to MCP server** → the scene currently held by the running MCP loads live (verified: 72 elements appeared in Layers). Then Export → **Video** (GIF/MP4); pick the **Camera frame** scope to follow the panning 16:9 camera rather than 'Complete drawing' which shows the whole scene strip zoomed out.

Caveat: the MCP server runs only while the Claude Code session is alive. After it stops, reconnect won't auto-restore the scene — reload the saved checkpoint first. To verify/automate from headless Chromium, the same localhost MCP connect works (Playwright can click 'Connect to MCP server' and the scene loads).

## Related

- [[Export a static .excalidraw from an Excalimate animated scene via get_scene]]
- [[Excalimate camera pan = translateX keyframes at each scene's center X]]
