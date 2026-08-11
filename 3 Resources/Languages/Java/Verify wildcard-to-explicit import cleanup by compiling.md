---
ai_hash: ca8f40612f0ca87f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11
status: seedling
tags:
- java
- imports
- gotcha
- luz-docs
title: Verify wildcard-to-explicit import cleanup by compiling
type: lesson
---

# Verify wildcard-to-explicit import cleanup by compiling

Replacing `import static X.*` with an explicit import list is only safe if verified by a compile: the rewrite can silently drop a symbol the file still uses, and (when done from memory or by tooling guess) add imports for symbols that do not exist at all.

Seen in luz_docs `MaterializeRepository.java`: an import cleanup replaced `MaterializeCascadeMarker.*` with two explicit imports but dropped `CASCADE_PARENT_CHANGE_FOLDER_IDS` (still used), and added three `MaterializeConstants` imports (`MATERIALIZE_PROJECTION`, `SNAPSHOT_COLLECTION`, `SNAPSHOT_DOCS`) that were never declared anywhere — both classes of error surfaced only as `cannot find symbol` at the next compile.

Rule: after any import rewrite, compile before moving on; grep the file body for each symbol you remove from a wildcard.

## Related

- [[Scrambled Java source shows as illegal-start-of-type errors mid-class]]

%% ai-graph-start %%

**Related notes:**
- [[Scrambled Java source shows as illegal-start-of-type errors mid-class]]
- [[Lombok one bad symbol cascades into hundreds of phantom missing-method errors]]
- [[A refactor that removes a method must grep tests for its name before merging]]
- [[luz_docs parent-change cascade recovers forward, not via snapshot rollback]]
- [[Run mvn test-compile after changing a recordctor signature — Cloud Build compiles tests, local mvn compile does not]]

%% ai-graph-end %%