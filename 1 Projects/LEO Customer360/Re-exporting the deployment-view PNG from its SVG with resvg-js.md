---
title: "Re-exporting the deployment-view PNG from its SVG with resvg-js"
created: 2026-08-19
type: howto
status: seedling
source: "session 2026-08-19"
tags: [customer360, excalidraw, svg, png, resvg, docs, diagram]
---

# Re-exporting the deployment-view PNG from its SVG with resvg-js

`leo-customer360/deployments/` keeps the UAT deployment diagram as THREE parallel files:
`deployment-view-uat.excalidraw` (editable JSON), `deployment-view-uat.svg`
(hand-authored vector, the real source of the image), and `deployment-view-uat.png`
(a **2x raster of the SVG**, 2800x2000 from a 1400x1000 viewBox). The PNG is derived from
the SVG, NOT from the excalidraw — keep all three in sync when the design changes.

**Re-export the PNG headlessly** (no browser, no ImageMagick — none installed on this box).
The SVG uses only standard Windows fonts (`Consolas, monospace` + `Segoe UI, system-ui`),
so `@resvg/resvg-js` (prebuilt Node binary, no native build) renders it faithfully:

```js
const {Resvg}=require('@resvg/resvg-js');
const fs=require('fs');
const svg=fs.readFileSync('deployment-view-uat.svg');
const img=new Resvg(svg,{fitTo:{mode:'width',value:2800},font:{loadSystemFonts:true}}).render();
fs.writeFileSync('deployment-view-uat.png', img.asPng());
```
`npm i @resvg/resvg-js@2` in a scratch dir first. The SVG already paints its own white bg
(`<rect width="100%" height="100%" fill="#ffffff"/>`), so no `background` option needed.

**Gotcha — the excalidraw JSON drifts.** A past "refresh" appended new label elements with
the SAME ids as the old ones instead of replacing them, leaving duplicate ids + a stale
`:9443/:19999 (SSO)` label. Fix = dedupe elements keeping the FIRST occurrence per id
(the first block held the correct current labels). The SVG/PNG were fine; only the
excalidraw needed cleaning.

Related: [[Portainer CSRF origin-invalid behind a reverse proxy - expose it directly]]
