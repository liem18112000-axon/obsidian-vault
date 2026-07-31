---
title: "Convert Excalidraw to draw.io by reading exported PNGs instead of the JSON"
created: 2026-07-23
type: howto
status: seedling
source: "session 2026-07-23 leocdp-personalization-engine"
tags: [drawio, excalidraw, diagrams, conversion]
---

# Convert Excalidraw to draw.io by reading exported PNGs instead of the JSON

When converting Excalidraw diagrams to draw.io XML, do not parse the verbose `.excalidraw` JSON — read the exported PNGs visually (as images) to recover layout, colors, and every text label, then hand-author the drawio cells. The JSON is 15-50 KB of noisy element properties per diagram; the PNG gives the same information at a glance.

Recreation cheat-sheet (drawio styles that cover the Excalidraw look):

- Rounded rectangle: `rounded=1;whiteSpace=wrap;html=1;fillColor=#...;strokeColor=#...;arcSize=14`
- Ellipse / diamond: `ellipse;...` / `rhombus;...`
- Free text: `text;html=1;strokeColor=none;fillColor=none;align=left`
- Dashed container frames (Excalidraw group boxes): a `rounded=1;fillColor=none;dashed=1;verticalAlign=top;align=left` rect used as a plain sibling — avoids drawio parent/child relative coordinates.
- Feedback-loop arrows: `edgeStyle=orthogonalEdgeStyle` plus explicit waypoints via `<Array as="points"><mxPoint x=".." y=".."/></Array>` inside the edge geometry; `dashed=1` for dashed loops.
- Code blocks: dark fill + `fontFamily=Courier New;align=left;spacingLeft=14`.

Escaping inside `value="..."` attributes (labels are HTML when `html=1`):

- line break = `&lt;br&gt;`, quote = `&quot;`, ampersand = `&amp;`, `<` in text = `&lt;` (e.g. `Hours()&lt;168`), leading indent in code = `&amp;nbsp;`.

Excalidraw palette ports well as: orange `#FFD8A8`/`#E8590C`, yellow `#FFF3BF`, purple `#E5DBFF`/`#7048E8`, blues `#4DABF7`/`#74C0FC`/`#D0EBFF`/`#1864AB`, green `#B2F2BB`/`#2B8A3E`, dark code box `#1E2A3A` with `#63E6BE` text.

Applied to merge 4 diagrams from `leocdp-personalization-engine/docs` into one `docs/leocdp-diagrams.drawio.xml` (4 pages). See [[A single draw.io file holds multiple diagrams as diagram pages in one mxfile]].

## Related

- [[A single draw.io file holds multiple diagrams as diagram pages in one mxfile]]
