---
title: "Verify wildcard-to-explicit import cleanup by compiling"
created: 2026-06-11
type: lesson
status: seedling
source: "session 2026-06-11"
tags: [java, imports, gotcha, luz-docs]
---

# Verify wildcard-to-explicit import cleanup by compiling

Replacing `import static X.*` with an explicit import list is only safe if verified by a compile: the rewrite can silently drop a symbol the file still uses, and (when done from memory or by tooling guess) add imports for symbols that do not exist at all.

Seen in luz_docs `MaterializeRepository.java`: an import cleanup replaced `MaterializeCascadeMarker.*` with two explicit imports but dropped `CASCADE_PARENT_CHANGE_FOLDER_IDS` (still used), and added three `MaterializeConstants` imports (`MATERIALIZE_PROJECTION`, `SNAPSHOT_COLLECTION`, `SNAPSHOT_DOCS`) that were never declared anywhere — both classes of error surfaced only as `cannot find symbol` at the next compile.

Rule: after any import rewrite, compile before moving on; grep the file body for each symbol you remove from a wildcard.

## Related

- [[Scrambled Java source shows as illegal-start-of-type errors mid-class]]
