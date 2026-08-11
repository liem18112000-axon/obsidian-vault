---
ai_hash: 1fa1c392f52d5c54
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack PrdComments.tsx
status: seedling
tags:
- dom
- selection
- range
- text-anchoring
title: Map a DOM selection to plain-text offsets with a pre-range
type: howto
---

# Map a DOM selection to plain-text offsets with a pre-range

To convert a user's DOM selection into offsets in the container's plain text (`element.textContent`), build a "pre-range": `pre.selectNodeContents(root); pre.setEnd(sel.startContainer, sel.startOffset)` — then `pre.toString().length` IS the start offset, and `start + selection.toString().length` is the end. Works because `Range.toString()` concatenates text nodes in document order, exactly like `textContent`.

The inverse (offsets → Range, e.g. to paint a stored highlight) walks text nodes with a `TreeWalker(root, NodeFilter.SHOW_TEXT)`, accumulating node lengths until the start/end offsets fall inside a node, then `setStart`/`setEnd` with the local remainder. This handles ranges spanning multiple elements (table cell → paragraph) natively.

Together these two mappings let you store text-quote anchors (position-independent) while still doing precise DOM work: selection capture on mouseup, highlight painting, and click-hit-testing (via `caretRangeFromPoint` + the pre-range trick).

## Related

- [[CSS Custom Highlight API paints text ranges without DOM mutation]]
- [[TextQuoteSelector anchoring survives document regeneration]]

%% ai-graph-start %%

**Related notes:**
- [[CSS Custom Highlight API paints text ranges without DOM mutation]]
- [[TextQuoteSelector anchoring survives document regeneration]]
- [[Whitespace-normalize with an index map to fuzzy-match yet return original offsets]]

%% ai-graph-end %%