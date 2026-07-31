---
title: "Parallel arrays in materialize sentinel preserve folderId order"
created: 2026-06-01
type: concept
status: seedling
source: "session 2026-06-01"
tags: [luz-docs, materialize, mongo, design-decision]
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
