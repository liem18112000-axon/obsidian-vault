---
title: "Merging guard-with-return into && drops the unconditional skip"
created: 2026-08-06
type: lesson
status: seedling
source: "luz_docs_import incident 2026-08-06"
tags: [java, control-flow, refactoring-hazard, linter, gotcha]
---

# Merging guard-with-return into && drops the unconditional skip

A "simplification" that folds a nested guard into a single `&&` can silently change behavior when the inner block is followed by an **unconditional** statement (a `return`/`continue`/skip).

```java
// CORRECT: skip EVERY metadata file; only orphans are also recorded as rejected
if (isMetadata(file)) {
    if (isOrphan(file)) { reject(file); }
    return;                       // fires for metadata whether orphan or not
}
createDocument(file);

// WRONG (what an auto-simplifier produced): only orphan metadata skipped
if (isMetadata(file) && isOrphan(file)) {
    reject(file);
    return;                       // never reached for non-orphan metadata
}
createDocument(file);             // <-- non-orphan metadata now wrongly imported
```

The two are NOT equivalent: the `return` in the first form is unconditional inside `if(A)`, so it also covers the `A && !B` case. Folding to `if (A && B)` moves that case to the fall-through path.

Real incident (luz_docs_import, 2026-08): companion `*.metadata.json` files (which must be consumed as metadata, never imported) were being uploaded as binary documents on the recipient's eArchive. Root cause: an aggressive auto-formatter/linter rewrote `if(isMetadata){ if(isOrphan){reject;} return; }` into `if(isMetadata && isOrphan){reject; return;}`. Only orphan metadata was skipped; every metadata file that HAD a matching document fell through to createDocument.

Lesson: when a guard clause ends in an unconditional skip/return, do NOT let it be merged with the inner condition. Review linter/auto-simplify diffs on control flow, and prefer a test that asserts the A-and-not-B path is skipped.

## Related

- [[luz_docs_import]]
