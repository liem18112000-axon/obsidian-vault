---
title: "Editing Excalidraw diagrams with committed SVG exports needs both files updated"
created: 2026-08-06
type: howto
status: seedling
source: "session 2026-08-06"
tags: [excalidraw, svg, diagrams, docs, gotcha]
---

# Editing Excalidraw diagrams with committed SVG exports needs both files updated

When a repo commits an Excalidraw diagram as **both** a `.excalidraw` source and a rendered `.svg` (the SVG is what a README `![](...)` actually shows), editing a label means updating **both files** — there is no build step that regenerates the SVG from the source. Miss the SVG and the docs still show the old text.

Two mechanics make targeted text edits reliable:

1. **In the `.excalidraw` JSON**, each text element stores the string **twice** — in `"text"` and in `"originalText"` (identical). Replace both (an exact-substring replace-all over the unique line does both at once).
2. **In the exported `.svg`**, each *wrapped line* of a multi-line label is a **separate `<text>` element** with the full line as its content — so match/replace per line, e.g. `>  :8080 keycloak   :3000 dagster</text>`. Colour (`fill`) often distinguishes semantic groups (e.g. host-facing labels in one colour vs internal ones in another), which lets you change only the intended lines.

Caveat: editing text without recomputing the element `width` (excalidraw) or box geometry (svg) can slightly overflow the surrounding box; fine for minor label tweaks, but reopen in excalidraw.com and re-export if precise layout matters.

Context: `leo-customer360` `k8s/docs/{kustomize-flow,pod-view}.{excalidraw,svg}` while remapping local kind host ports.

## Related

- [[kind port remap only hostPort binds the host; NodePorttargetPort stay internal]]
