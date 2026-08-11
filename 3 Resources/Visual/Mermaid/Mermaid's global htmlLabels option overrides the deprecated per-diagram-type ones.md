---
ai_hash: 5a2d3e3b997fa9ae
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack session 2026-07-08
status: seedling
tags:
- mermaid
- configuration
- gotcha
title: Mermaid's global htmlLabels option overrides the deprecated per-diagram-type
  ones
type: lesson
---

# Mermaid's global htmlLabels option overrides the deprecated per-diagram-type ones

In Mermaid 11.x, `htmlLabels` can be set two ways: a deprecated per-diagram-type key (`flowchart: { htmlLabels: false }`, and similarly for `class`/`state`/etc.), or a global top-level key (`htmlLabels: false` as a sibling of `flowchart`, not nested inside it).

Only the **global top-level key actually worked** when tested live (Mermaid 11.15, in a real browser). The deprecated per-type key had no visible effect on the rendered output, even though Mermaid's own bundled source shows an apparent fallback chain (`config.htmlLabels ?? config.flowchart?.htmlLabels ?? true`) that looks like it should honor the per-type key.

Lesson: don't trust that a config option "should" work from reading a library's source/fallback logic alone — verify by inspecting the actual rendered output (e.g. check the generated SVG for absence of `<foreignObject>`) after a real render, especially for options a library has marked deprecated.

Related: [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]

## Related

- [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]

%% ai-graph-start %%

**Related notes:**
- [[Mermaid defaults to foreignObject HTML labels, breaking canvas export]]
- [[Mermaid text clipping causes useMaxWidth shrink, narrow wrappingWidth, and web-font race]]
- [[SVG foreignObject taints a canvas on drawImage]]
- [[Mermaid render() leaks its error-bomb SVG into the DOM past a caught throw; fix with suppressErrorRendering]]
- [[Mermaid Flowchart - Multi-word Labels and Decision Branches]]

%% ai-graph-end %%