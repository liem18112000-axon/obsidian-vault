---
ai_hash: 6a703bdca69660af
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: session 2026-06-01
status: seedling
tags:
- luz-docs
- materialize
- mongo
- design-decision
title: Parallel arrays in materialize sentinel preserve folderId order
type: concept
---

# Parallel arrays in materialize sentinel preserve folderId order

In `MaterializeCompute.compute`, three sentinel arrays — `_folderIds`, `_folderNames`, `_folderSecurityClassCodes` — are kept in lockstep so that `MaterializeResponseBuilder.addFoldersField` can rebuild `_folders[]` by index without any ordering map.

**Why parallel arrays over a per-id object map:**
- folderId order is load-bearing for the response: the legacy `$lookup`-based path returned `_folders` in the document's `folderIds` order, and callers depend on that ordering.
- The caller's `foldersById` is a `LinkedHashMap`, which Java guarantees iterates in insertion order. Compute walks that order and pushes into the three arrays in lockstep.
- A folder that fails to resolve (`Optional.empty()` branch) still pushes an empty-string name and empty codes list, so the index alignment never breaks.

**Tradeoff:** a future caller that needs codes-by-folder-id pays an O(n) scan (or has to zip the arrays themselves). Acceptable here because all consumers iterate position-wise, not by-id.

**Contrast — legacy aggregate:** the `$lookup` did the lookup live and embedded per-folder codes per object, so order was implicit. Materialized path can't afford the live lookup, hence the parallel-array trick.

## Related
[[MaterializeState]]
[[Adding a field to a Java record breaks all factory and constructor calls in tests]]

%% ai-graph-start %%

**Related notes:**
- [[Empty per-folder codes means public, not no-access]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[_folderNames is parent-chain-independent — depends only on each folder's own name]]
- [[Missing folder reference produces fail-closed materialize state]]
- [[Fail-closed defense over a parallel array distinguish present-but-short from absent]]

%% ai-graph-end %%