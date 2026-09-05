---
title: "Excalidraw fontFamily codes + .excalidraw/.png can drift out of sync"
created: 2026-08-22
type: lesson
status: seedling
source: "session 2026-08-22"
tags: [excalidraw, diagram, fonts, gotcha, rendering]
---

# Excalidraw fontFamily codes + .excalidraw/.png can drift out of sync

Two things that bit me editing an existing `.excalidraw` deployment diagram.

## fontFamily codes (what makes a diagram look hand-drawn vs clean)
- **1 = Virgil** — the hand-drawn/sketchy font. Combined with `roughness: 1` (sketchy edges) this is the informal look many find "ugly" for architecture docs.
- **2 = Helvetica/Nunito** — clean sans-serif. Use for box labels/titles you want crisp.
- **3 = Cascadia Code** — monospace. Good for ports, IDs, code-ish details.
To "de-uglify": bulk-set text `fontFamily 1 -> 2` and every shape/arrow/line `roughness -> 0`.

## The .excalidraw source and its exported .png/.svg can drift
A repo can commit an `.excalidraw` that does NOT match its committed `.png`: e.g. the PNG was a polished export (fontFamily 2, roughness 0, arrows rerouted) but the committed `.excalidraw` is an older rough draft (fontFamily 1, roughness 1, diagonal arrows through boxes). Re-rendering the source then "regresses" the image. Fix by treating the `.excalidraw` as the source of truth and polishing IT, then re-export both png+svg with the skill's `render_excalidraw.py --format both`.

## Arrow-through-box cleanup
The excalidraw-diagram skill's rule: walk every arrow segment vs every non-endpoint box. Diagonal arrows from a far source to a far target (e.g. LB -> a bottom monitoring box) cut through middle boxes — reroute them down the clear left/right MARGIN lanes (waypoints just inside the container border, outside all child boxes), and move their labels to the margin too. Crossing a *container* rectangle (VPC/box-group) to reach a child inside it is fine; crossing a *sibling* box is not.

## Related
[[Serve a no-auth UI over TLS+SSO behind Caddy at a subpath (oauth2-proxy proxy-prefix)]]
