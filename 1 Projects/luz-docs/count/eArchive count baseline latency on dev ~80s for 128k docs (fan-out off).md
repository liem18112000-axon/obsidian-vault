---
title: "eArchive count baseline latency on dev: ~80s for 128k docs (fan-out off)"
created: 2026-06-19
type: observation
status: seedling
source: "session 2026-06-19"
tags: [luz-docs, performance, count, dev, measurement]
---

# eArchive count baseline latency on dev: ~80s for 128k docs (fan-out off)

Measured on the dev tenant `d0783310-d67f-4ab7-9aab-dcaef3f17f48` (128,000 documents, 8-12 folders) on 2026-06-19: a single eArchive `POST /{tenant}/documents/count` call takes **~73-93 seconds** (3 consecutive probes: 77.9s, 73.2s, 92.8s; all returned 200, total=128000).

**Implications:**
- This is ~20x over the 4s performance target the IT `count_document.feature` asserts. The scenario will FAIL the target on this tenant as-is.
- The ~80s latency is consistent with a **single full count** (parallel fan-out OFF / not effective here): config `luz.docs.materialize.count-fanout-partitions` defaults to 1 = disabled. See [[luz-docs parallelized count undercounts documents missing _shard]].
- **Gotcha for the IT:** the rest client `DEFAULT_TIMEOUT = 60` (`api/client/luz_docs_rest_client.py`) is SHORTER than the count latency, so `count_documents` raises ReadTimeout / the run errors before it can even record a timing. Any real measurement of this endpoint needs a higher per-request timeout.
- **Infra gotcha:** `kubectl port-forward service/api-forwarder` drops under sustained heavy requests (10 sequential ~80s counts killed the forward mid-run with RemoteDisconnected). Restart it before long perf runs; bind to localhost only (0.0.0.0 is blocked by the sandbox).

## Related

- [[luz-docs parallelized count undercounts documents missing _shard]]


## Root cause (2026-06-19)
- Deployed dev luz-docs image = commit `cf8687633` (2026-06-18). `git ls-tree -r cf8687633 | grep parallelize` => **NOT present**: the `ParallelizeCount` fan-out code is NOT in the running binary.
- The parallelize code lives in later commits `5e37c1353` (fan-out) + `df3cb3bbc` (fail-soft) on the branch, AFTER the deployed commit.
- Configmap `luz-docs-env-configmap-dg5bm564h8` already has `LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS: "6"` and `LUZ_DOCS_TENANTS_USE_MATERIALIZED: "*"` => **config provisioned ahead of code**. The partitions=6 setting is unused because the class reading it is not deployed; counts still use the old single materialized count => ~80s.
- **Lesson:** `git branch --contains <sha>` only proves the sha is an ANCESTOR of the branch, NOT that the sha includes a later feature. To check if a deployed image has a feature, inspect the tree at that exact sha (`git ls-tree`), do not rely on --contains.
- **Next:** build + roll out the branch to dev, then re-measure the 4s target.

## After deploying parallelize branch (fan-out=6, 2026-06-19)
- Rolled out commit 42830e38b (ParallelizeCount present). Re-measured count on same tenant (128k docs):
  - run1 (cold) 94.7s; warm runs 14.3 / 21.7 / 15.8 / 20.9s; avg 33.5s, warm-avg ~18s.
- **Fan-out gives ~4x speedup (80s -> ~18s warm) but does NOT reach the 4s target.** Cold first call still ~95s (JVM/cache cold).
- Conclusion: 4s target likely needs the sibling branch `LUZ-154613-f-cache-count` (count caching), not fan-out alone. More partitions and/or a `_shard` index may help but ~18s warm suggests count is scan-bound.
