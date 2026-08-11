---
ai_hash: 5ffaa72c83a757f4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack PRD inline-comments plan
status: seedling
tags:
- css
- react
- highlighting
- selection
title: CSS Custom Highlight API paints text ranges without DOM mutation
type: howto
---

# CSS Custom Highlight API paints text ranges without DOM mutation

To highlight arbitrary text ranges in React-rendered content (e.g. comment anchors over ReactMarkdown output), use the **CSS Custom Highlight API** (`CSS.highlights.set(name, new Highlight(...ranges))` + `::highlight(name)` CSS) instead of wrapping text in `<mark>` elements.

Why it wins:
- **No DOM mutation** — React re-renders don't fight your injected elements, and you never corrupt React's virtual-DOM bookkeeping.
- **Multi-node ranges work natively** — a selection spanning a table cell and a paragraph is one `Range`; `<mark>`-wrapping would require splitting per text node.
- Supported in all evergreen browsers and Chromium ≥ 105 (so current Electron too).

Fallback plan if the runtime is older: skip inline painting entirely (sidebar-only comments) rather than doing DOM surgery.

## Related

- [[TextQuoteSelector anchoring survives document regeneration]]

%% ai-graph-start %%

**Related notes:**
- [[Map a DOM selection to plain-text offsets with a pre-range]]
- [[TextQuoteSelector anchoring survives document regeneration]]

%% ai-graph-end %%