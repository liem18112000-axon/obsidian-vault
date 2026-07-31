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

Why you'd be on `htmlLabels: false` at all: it is usually set to keep canvas PNG export working (see [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]), not for wrapping reasons. So if you need both PNG export AND well-wrapped text, you must reconcile causes #1–3 rather than reverting to HTML labels.

## Related

- [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]
- [[Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones]]
