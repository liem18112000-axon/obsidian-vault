---
ai_hash: 8f23af7c0a8485c8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities:
- eArchive
- latency
- dev
- 128k docs
- fan-out
- deployed image `cf8687633`
- branch `42830e38b`
- ParallelizeCount
- K=6
- ~80s
- ~18s warm
- 4s target
- IT `count_document.feature`
- scan-bound
- '`LUZ-154613-f-cache-count`'
- count caching
- configmap
- '`luz-docs-env-configmap-dg5bm564h8`'
- '`LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS`'
- '`LUZ_DOCS_TENANTS_USE_MATERIALIZED`'
- '`5e37c1353`'
- '`df3cb3bbc`'
- '`git branch --contains <sha>`'
- '`git ls-tree -r <sha>`'
- IT rest client
- '`DEFAULT_TIMEOUT`'
- count latency
- '`ReadTimeout`'
- '`kubectl port-forward`'
- '`api-forwarder`'
- sustained load
- '`RemoteDisconnected`'
- localhost
- '`0.0.0.0`'
- sandbox
- '`luz-docs parallelized count`'
- '`documents missing _shard`'
- '`luz_docs documentscount`'
- sub-second
- '`POST /{tenant}/documents/count`'
- '`d0783310-d67f-4ab7-9aab-dcaef3f17f48`'
- '`10 sequential ~80s counts`'
source: session 2026-06-19
status: seedling
tags:
- luz-docs
- performance
- count
- dev
- measurement
title: 'eArchive count baseline latency on dev: ~80s for 128k docs (fan-out off)'
type: observation
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
- [[1 Projects/luz-docs/luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[eArchive 800k bottleneck is view-controller not K]]
- [[luz_docs documentscount is scan-bound and cannot reach sub-second at 128k]]
- [[eArchive request flow and log correlation (perf)]]
- [[Dev benchmark _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain]]

**Relations:**
- eArchive — *has baseline latency* — ~80s
- ~80s — *observed on* — dev
- ~80s — *for* — 128k docs
- dev — *uses tenant* — `d0783310-d67f-4ab7-9aab-dcaef3f17f48`
- `d0783310-d67f-4ab7-9aab-dcaef3f17f48` — *has* — 128k docs
- `POST /{tenant}/documents/count` — *is endpoint for* — count latency
- deployed image `cf8687633` — *has no* — fan-out
- deployed image `cf8687633` — *resulted in latency* — ~80s
- branch `42830e38b` — *implements* — ParallelizeCount
- ParallelizeCount — *uses* — fan-out
- fan-out — *configured with partitions* — K=6
- branch `42830e38b` — *resulted in warm latency* — ~18s warm
- ~18s warm — *is ~4x faster than* — ~80s
- ~18s warm — *indicates* — scan-bound
- 4s target — *asserted by* — IT `count_document.feature`
- ~18s warm — *is ~20x over* — 4s target
- 4s target — *likely needs* — `LUZ-154613-f-cache-count`
- `LUZ-154613-f-cache-count` — *is a type of* — count caching
- configmap — *named* — `luz-docs-env-configmap-dg5bm564h8`
- `luz-docs-env-configmap-dg5bm564h8` — *carried config* — `LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS`
- `luz-docs-env-configmap-dg5bm564h8` — *carried config* — `LUZ_DOCS_TENANTS_USE_MATERIALIZED`
- fan-out — *landed in commit* — `5e37c1353`
- fan-out — *landed in commit* — `df3cb3bbc`
- `git branch --contains <sha>` — *proves ancestry of* — sha
- `git ls-tree -r <sha>` — *inspects tree at* — sha
- IT rest client — *has* — `DEFAULT_TIMEOUT`
- `DEFAULT_TIMEOUT` — *is shorter than* — count latency
- count latency — *causes* — `ReadTimeout`
- `kubectl port-forward` — *drops under* — sustained load
- sustained load — *caused by* — `10 sequential ~80s counts`
- `kubectl port-forward` — *fails with* — `RemoteDisconnected`
- `kubectl port-forward` — *binds to* — localhost
- `0.0.0.0` — *is blocked by* — sandbox
- `luz-docs parallelized count` — *undercounts* — `documents missing _shard`
- `luz_docs documentscount` — *is* — scan-bound
- `luz_docs documentscount` — *cannot reach* — sub-second
- `luz_docs documentscount` — *for* — 128k docs

%% ai-graph-end %%