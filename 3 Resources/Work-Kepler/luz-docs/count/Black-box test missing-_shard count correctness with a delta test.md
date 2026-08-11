---
ai_hash: 75bfd45d7f5ba18a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- luz-docs
- integration-test
- behave
- testing-technique
title: Black-box test missing-_shard count correctness with a delta test
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[Fan-out gate and backfill filter must cover the same field set]]

%% ai-graph-end %%