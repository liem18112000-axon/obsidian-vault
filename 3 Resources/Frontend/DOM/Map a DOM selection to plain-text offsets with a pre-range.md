---
title: "Map a DOM selection to plain-text offsets with a pre-range"
created: 2026-07-03
type: howto
status: seedling
source: "session 2026-07-03, vinnstack PrdComments.tsx"
tags: [dom, selection, range, text-anchoring]
---

# Map a DOM selection to plain-text offsets with a pre-range

To convert a user's DOM selection into offsets in the container's plain text (`element.textContent`), build a "pre-range": `pre.selectNodeContents(root); pre.setEnd(sel.startContainer, sel.startOffset)` — then `pre.toString().length` IS the start offset, and `start + selection.toString().length` is the end. Works because `Range.toString()` concatenates text nodes in document order, exactly like `textContent`.

The inverse (offsets → Range, e.g. to paint a stored highlight) walks text nodes with a `TreeWalker(root, NodeFilter.SHOW_TEXT)`, accumulating node lengths until the start/end offsets fall inside a node, then `setStart`/`setEnd` with the local remainder. This handles ranges spanning multiple elements (table cell → paragraph) natively.

Together these two mappings let you store text-quote anchors (position-independent) while still doing precise DOM work: selection capture on mouseup, highlight painting, and click-hit-testing (via `caretRangeFromPoint` + the pre-range trick).

## Related

- [[CSS Custom Highlight API paints text ranges without DOM mutation]]
- [[TextQuoteSelector anchoring survives document regeneration]]
