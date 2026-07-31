---
title: "eArchive count baseline latency on dev: ~80s for 128k docs (fan-out off)"
created: 2026-06-19
type: observation
status: seedling
source: "session 2026-06-19"
tags: [luz-docs, performance, count, dev, measurement]
---

# eArchive count baseline latency on dev: ~80s for 128k docs (fan-out off)

Dev tenant `d0783310-d67f-4ab7-9aab-dcaef3f17f48` (128,000 docs, 8-12 folders), 2026-06-19. `POST /{tenant}/documents/count`:

| Build | Result |
|---|---|
| deployed image `cf8687633` (no fan-out code) | 77.9 / 73.2 / 92.8 s — **~80s**, all 200, total=128000 |
| branch `42830e38b` (ParallelizeCount, fan-out K=6) | cold 94.7 s; warm 14.3 / 21.7 / 15.8 / 20.9 s — **~18s warm, ~4x** |

**~20x over the 4s target** the IT `count_document.feature` asserts, before and after fan-out. ~18s warm says the count is scan-bound; the 4s target likely needs the sibling branch `LUZ-154613-f-cache-count` (count caching), not fan-out alone.

**Why the baseline had no fan-out — config provisioned ahead of code.** Configmap `luz-docs-env-configmap-dg5bm564h8` already carried `LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS: "6"` and `LUZ_DOCS_TENANTS_USE_MATERIALIZED: "*"`, but the class reading them was not in the deployed commit (fan-out landed later, in `5e37c1353` + `df3cb3bbc`).
**Lesson:** `git branch --contains <sha>` only proves the sha is an ANCESTOR of the branch, not that it includes a later feature — inspect the tree at that exact sha (`git ls-tree -r <sha>`) to check whether a deployed image has a feature.

**Two gotchas for measuring this endpoint:**
- The IT rest client's `DEFAULT_TIMEOUT = 60` (`api/client/luz_docs_rest_client.py`) is shorter than the count latency, so `count_documents` raises ReadTimeout before it can record a timing. Raise the per-request timeout.
- `kubectl port-forward service/api-forwarder` drops under sustained load (10 sequential ~80s counts killed it with RemoteDisconnected). Restart before long perf runs; bind to localhost only (0.0.0.0 is blocked by the sandbox).

## Related

- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[luz_docs /documents/count is scan-bound and cannot reach sub-second at 128k]]
