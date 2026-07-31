---
title: "Render Excalidraw-style hand-drawn PNGs headlessly with rough.js in the Playwright browser"
created: 2026-07-25
type: howto
status: seedling
source: "session 2026-07-25"
tags: [excalidraw, roughjs, playwright, canvas, png, diagrams, technique]
---

# Render Excalidraw-style hand-drawn PNGs headlessly with rough.js in the Playwright browser

To produce an Excalidraw-style (hand-drawn) PNG without the full Excalidraw React runtime, drive the Playwright MCP browser and draw with **rough.js** -- the same sketch engine Excalidraw uses -- onto a 2D canvas, then `canvas.toDataURL()`.

Recipe that worked:
- Navigate to `about:blank` first. rough.js is injected via a `<script src=unpkg>` tag; excalidraw.com and most sites set a CSP that blocks injecting cross-origin scripts, but about:blank has no CSP.
- Load the hand-drawn font with the FontFace API: `new FontFace("ExHand", "url(https://unpkg.com/@excalidraw/excalidraw@0.17.6/dist/excalidraw-assets/Virgil.woff2)")` then `document.fonts.add(ff)`. unpkg sends permissive CORS so the fetch succeeds. Fall back to `"Comic Sans MS","Segoe Print",cursive` (Comic Sans ships on Windows) if it fails.
- Canvas: set `canvas.width = W*2` and `ctx.scale(2,2)` for a crisp 2x export; fill white first.
- Shapes: `const rc = rough.canvas(canvas); rc.rectangle(x,y,w,h,{fill,fillStyle:"solid",stroke,strokeWidth:2,roughness:1.1, strokeLineDash:[8,6]})`. Arrows = `rc.line(...)` plus two short lines at the tip (angle +/-0.42 rad) for the arrowhead. Text is NOT rough.js -- use `ctx.fillText` in the hand font; draw a white rect behind edge labels for legibility.

**Key gotcha -- avoid context bloat:** a returned base64 PNG dataURL is ~750KB of text. Do NOT return it inline. Set the `filename` param on `browser_evaluate` so the result is written to a file on disk (the MCP JSON-encodes it there), then a Node script reads that file, `JSON.parse`s it, strips the `data:image/png;base64,` prefix, and `Buffer.from(b64,"base64")` -> `fs.writeFileSync`. Write with a Windows-style path (`C:/Users/...`), not a Git-Bash `/c/...` path, or Node resolves it to `C:\c\Users\...` and ENOENTs.

Best paired with generating the matching `.drawio` (mxGraph XML) and `.excalidraw` scene from one shared node/edge model so all three stay consistent. Used to diagram a docker-compose stack (core-customer360). See [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]] for the same browser_evaluate + filename trick.

## Related

- [[Edit Confluence Cloud via authenticated Playwright browser when the Atlassian MCP app is not installed]]
