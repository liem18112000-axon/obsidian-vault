---
ai_hash: 2d64bca7a6f80a05
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-24
entities: []
source: appsflyer-data-connector docs, 2026-06-24
status: seedling
tags:
- svg
- png
- sharp
- nodejs
- excalidraw
- rendering
- offline
title: Rasterize SVG to PNG offline with Node sharp (and Excalidraw via hand-SVG)
type: howto
---

# Rasterize SVG to PNG offline with Node sharp (and Excalidraw via hand-SVG)

To rasterize an SVG (or a hand-derived Excalidraw diagram) to PNG **fully offline** with only Node available, use the **\`sharp\`** package — it ships prebuilt libvips binaries that load SVG and write PNG, no system librsvg/Inkscape/ImageMagick needed:

\`\`\`js
require("sharp")("in.svg", { density: 150 }).resize(1500).png().toFile("out.png")
\`\`\`

- \`density\` (DPI) controls the rasterization resolution of the vector before any resize; bump it for crisper output, then \`.resize(width)\` to the final pixel width.
- Install in a scratch dir (\`npm i sharp\`) so the target repo stays clean.
- For an Excalidraw file: its \`.excalidraw\` is JSON of simple primitives (rectangle/ellipse/arrow/text). With \`roughness:0\` the shapes are clean lines, so hand-writing an equivalent SVG with the same coordinates/colors renders an essentially faithful PNG — handy when the official Excalidraw export (browser/puppeteer based) is unavailable offline.

See [[3 Resources/Tooling/Windows/Windows 'convert' is NTFS convert.exe, not ImageMagick]].

## Related

- [[3 Resources/Tooling/Windows/Windows 'convert' is NTFS convert.exe, not ImageMagick]]

%% ai-graph-start %%

**Related notes:**
- [[Render Excalidraw-style hand-drawn PNGs headlessly with rough.js in the Playwright browser]]
- [[Render .excalidraw to PNG headlessly with excalidraw-brute-export-cli]]
- [[Copy an SVG diagram to the clipboard as PNG - viewBox-sized canvas rasterization]]

%% ai-graph-end %%