---
title: "Pick the variant matching the data you already hold, not the triggering operation"
created: 2026-06-04
type: lesson
status: seedling
source: "session 2026-06-04, luz_docs"
tags: [design, adapter-pattern, api-design]
---

# Pick the variant matching the data you already hold, not the triggering operation

When a class hierarchy offers variants that differ **only in how they adapt input** (e.g. a Patch-shaped adapter that converts to a Put-shaped call), choose the variant whose constructor matches the data you already have in hand — not the one named after the operation you're conceptually performing.

**Why:** adapter variants pay real costs to convert: in luz_docs, the Patch process re-fetches the document from Mongo just to build the before/after pair the Put process needs. Calling the adapter when you already hold the converted form means fabricating fake input (synthetic patch ops) and paying extra I/O to end up at the same code path.

**Heuristic:** if using variant A would require you to *construct* its input format from data that already fits variant B's input format, call B directly.

## Related
- [[Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]
