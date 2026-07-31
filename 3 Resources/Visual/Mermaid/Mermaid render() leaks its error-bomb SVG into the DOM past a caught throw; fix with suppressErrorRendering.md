---
title: "Mermaid render() leaks its error-bomb SVG into the DOM past a caught throw; fix with suppressErrorRendering"
created: 2026-07-19
type: lesson
status: seedling
source: "Vinnstack session 2026-07-19"
tags: [mermaid, frontend, gotcha, vinnstack]
---

# Mermaid render() leaks its error-bomb SVG into the DOM past a caught throw; fix with suppressErrorRendering

Mermaid's `render(id, text)` injects its own "Syntax error in text / mermaid version X" bomb SVG **directly into the DOM** on a failed render — and it does so even when you `await mermaid.render(...)` inside a `try/catch` and swallow the throw. So a component with a perfectly good error fallback can still show mermaid bomb graphics leaking onto the page.

Why the try/catch is not enough: `mermaid.parse(src)` can PASS while `render()` later fails during layout (e.g. a flowchart whose grammar is valid but dagre layout chokes). At that point render has already appended the error element before it throws, so catching the throw does not remove it.

**Fix:** set `suppressErrorRendering: true` in `mermaid.initialize({...})` (available since v10.6, present in v11). render() still throws so your own catch/fallback runs, but mermaid no longer injects the bomb SVG. Then your raw-source `<pre>` fallback is what the user sees on any unparseable or half-streamed diagram.

Found live in Vinnstack (`components/ui/Mermaid.tsx`, mermaid 11.15.0) — four Process-Flow diagrams showed four "Syntax error in text mermaid version 11.15.0" bombs despite the component already catching render throws and falling back to a `<pre>`.

Related: [[Streaming LLM mermaid needs a parse-gate before render]] — the same component gates on `mermaid.parse` so half-streamed diagrams show source, not garbage.

## Related

- [[Streaming LLM mermaid needs a parse-gate before render]]
