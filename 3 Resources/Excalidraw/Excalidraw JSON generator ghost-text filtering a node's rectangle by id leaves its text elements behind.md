---
title: "Excalidraw JSON generator ghost-text: filtering a node's rectangle by id leaves its text elements behind"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25"
tags: [excalidraw, diagrams, json, gotcha]
---

# Excalidraw JSON generator ghost-text: filtering a node's rectangle by id leaves its text elements behind

When a script builds Excalidraw `.excalidraw` JSON, a single logical "node" is usually **multiple elements**: a `rectangle` element **plus one or more separate `text` elements** (title, description) that are positioned over it (or bound via `containerId`).

**The trap:** deleting/repositioning a node by filtering the elements array on the rectangle's id —
```js
elements = elements.filter(e => e.id !== "s3_ag");
```
— removes only the rectangle. The associated text elements have their own `txt...` ids and **survive**, rendering as faint "ghost" text stuck at the old coordinates.

**Fix:** place each node exactly once at its final coordinates. If you must remove a node after the fact, also remove its text elements (track their ids, or give the text a `containerId`/`groupId` you can filter on). Simplest is to never create it in the wrong place to begin with.

## Related

- [[claude.ai share links can be org-restricted and require login]]
