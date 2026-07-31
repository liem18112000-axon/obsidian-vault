---
title: "Mermaid text clipping causes: useMaxWidth shrink, narrow wrappingWidth, and web-font race"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack session 2026-07-08"
tags: [mermaid, svg, rendering, frontend, gotcha]
---

# Mermaid text clipping causes: useMaxWidth shrink, narrow wrappingWidth, and web-font race

Mermaid diagram text that appears cut off or shrunk to unreadable size usually traces to one or more of three separate causes stacking together — worth checking all three, not just the first one found.

1. **`useMaxWidth` (default `true`) shrinks the whole finished SVG to its container's width.** Mermaid renders the SVG with `width="100%"` and an inline `max-width` style, so a diagram wider than its box gets scaled down uniformly — including all text — rather than the container scrolling. Set `flowchart: { useMaxWidth: false }` (and `sequence: { useMaxWidth: false }` etc. per diagram type) to render at natural intrinsic size instead, and let a CSS `overflow: auto` wrapper scroll it.

2. **A CSS rule like `[&_svg]:max-w-full` on the container fights the fix above.** Even with `useMaxWidth: false` on the Mermaid side, if the consuming component also caps the rendered `<svg>` at `max-width: 100%`, it gets shrunk right back down. Both the Mermaid config AND any container CSS that caps SVG width have to be fixed together.

3. **With `htmlLabels: false` (SVG `<text>` labels instead of HTML `<div>` labels), Mermaid wraps text itself at `flowchart.wrappingWidth` (default 200px) and sizes each node's box to fit that wrap.** The default is quite narrow, so labels wrap aggressively into many lines; combined with cause #1, an already-tightly-wrapped diagram gets shrunk further and reads as clipped. Raising `wrappingWidth` (e.g. to 300) reduces how aggressively it wraps.

4. **Separately: if a custom `fontFamily` in `themeVariables` hasn't finished loading when `mermaid.render()` first runs, Mermaid measures label text against the browser's fallback font to size nodes — then the real (usually wider) font paints after it loads, overflowing the box it was sized for.** Await `document.fonts.ready` before the first render call to eliminate this race.

## Why `htmlLabels: false` matters here at all

`htmlLabels: false` is often chosen NOT for text-wrapping reasons but to fix a completely different bug: an SVG containing `<foreignObject>` (which is how Mermaid's default HTML labels are embedded) permanently taints an HTML `<canvas>` the instant it's drawn onto it — `canvas.toBlob()`/`toDataURL()` then throw, even from a same-origin `blob:` URL. So "copy diagram as PNG" breaks silently on every diagram with a text label if `htmlLabels` is left at its default `true`. If you need both PNG export AND well-wrapped text, you're stuck reconciling causes #1-3 (SVG-text wrapping) rather than reverting to HTML labels.

## Related
- [[Decouple internal PK from external ticket ID for draft-before-push records]]
