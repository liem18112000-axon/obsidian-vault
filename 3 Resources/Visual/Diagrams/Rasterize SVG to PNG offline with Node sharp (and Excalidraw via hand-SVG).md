---
title: "Rasterize SVG to PNG offline with Node sharp (and Excalidraw via hand-SVG)"
created: 2026-06-24
type: howto
status: seedling
source: "appsflyer-data-connector docs, 2026-06-24"
tags: [svg, png, sharp, nodejs, excalidraw, rendering, offline]
---

# Rasterize SVG to PNG offline with Node sharp (and Excalidraw via hand-SVG)

To rasterize an SVG (or a hand-derived Excalidraw diagram) to PNG **fully offline** with only Node available, use the **\`sharp\`** package — it ships prebuilt libvips binaries that load SVG and write PNG, no system librsvg/Inkscape/ImageMagick needed:

\`\`\`js
require("sharp")("in.svg", { density: 150 }).resize(1500).png().toFile("out.png")
\`\`\`

- \`density\` (DPI) controls the rasterization resolution of the vector before any resize; bump it for crisper output, then \`.resize(width)\` to the final pixel width.
- Install in a scratch dir (\`npm i sharp\`) so the target repo stays clean.
- For an Excalidraw file: its \`.excalidraw\` is JSON of simple primitives (rectangle/ellipse/arrow/text). With \`roughness:0\` the shapes are clean lines, so hand-writing an equivalent SVG with the same coordinates/colors renders an essentially faithful PNG — handy when the official Excalidraw export (browser/puppeteer based) is unavailable offline.

See [[Windows convert is NTFS convert.exe, not ImageMagick]].

## Related

- [[Windows convert is NTFS convert.exe, not ImageMagick]]
