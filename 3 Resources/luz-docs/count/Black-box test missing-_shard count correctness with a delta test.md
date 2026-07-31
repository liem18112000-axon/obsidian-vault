---
title: "Black-box test missing-_shard count correctness with a delta test"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [luz-docs, integration-test, behave, testing-technique]
---

# Black-box test missing-_shard count correctness with a delta test

To verify "documents missing a `_shard` field are still counted correctly" from an **integration test** (black-box, API-only), use a **delta test** instead of trying to assert an absolute count:

1. Record a **baseline** count with the target query.
2. **Create K** documents that match the query (attach `folderIds` so they satisfy an `exists folderIds` clause).
3. **Recount** with the same query.
4. Assert the total rose by **exactly K**.

**Why:** the IT cannot set the internal `_shard` field via the public API, so it can't directly construct "un-stamped" docs or read an independent oracle. Freshly created docs are not shard-stamped on a non-materialize tenant, so the delta reproduces the missing-`_shard` condition; a fan-out bug that drops them shows up as delta < K. The delta is also robust to other activity on a large shared tenant in a way an absolute-count assertion is not.

**How to apply (behave):** register cleanup with `context.add_cleanup(...)` right after creating the folder/docs so they are permanently deleted even if an assertion fails. Implemented in `features/document/count/count_document.feature` + `features/steps/count_document_steps.py`.

Related: [[luz-docs parallelized count undercounts documents missing _shard]]

## Related

- [[luz-docs parallelized count undercounts documents missing _shard]]
