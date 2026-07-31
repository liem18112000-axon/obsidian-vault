---
title: "Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack session 2026-07-08"
tags: [mermaid, configuration, gotcha]
---

# Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones

In Mermaid 11.x, `htmlLabels` can be set two ways: a deprecated per-diagram-type key (`flowchart: { htmlLabels: false }`, and similarly for `class`/`state`/etc.), or a global top-level key (`htmlLabels: false` as a sibling of `flowchart`, not nested inside it).

Only the **global top-level key actually worked** when tested live (Mermaid 11.15, in a real browser). The deprecated per-type key had no visible effect on the rendered output, even though Mermaid's own bundled source shows an apparent fallback chain (`config.htmlLabels ?? config.flowchart?.htmlLabels ?? true`) that looks like it should honor the per-type key.

Lesson: don't trust that a config option "should" work from reading a library's source/fallback logic alone — verify by inspecting the actual rendered output (e.g. check the generated SVG for absence of `<foreignObject>`) after a real render, especially for options a library has marked deprecated.

Related: [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]

## Related

- [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]
