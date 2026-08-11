---
ai_hash: 9736fe6911889d85
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities:
- Dev benchmark
- _shard count fan-out
- K=12
- local port-forward
- luz-docs
- LUZ-154613
- '2026-06-17'
- 128k docs tenant
- deployed image
- real service mesh
- K=1
- K=4
- K=8
- K=16
- speedup
- pod cores
- real infra
- local docker-compose run
- kubectl port-forward
- concurrent sub-counts
- mongo explain
- shared dev cluster
- K 8–12
- current dev pod
- _shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the
  speedup
- Partition the materialized count on a uniform _shard int
- not _id
source: LUZ-154613 session 2026-06-17
status: seedling
tags:
- luz-docs
- benchmark
- performance
- fanout
- materialize
title: 'Dev benchmark: _shard count fan-out ~1.8x, diminishing past K=12; local port-forward
  hid the gain'
type: observation
---

# Dev benchmark: _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain

Dev benchmark of the luz-docs _shard count fan-out (LUZ-154613, 2026-06-17, tenant 128k docs, deployed image, real service mesh). Same query swept over K=1/4/8/12/16, count exact 128000 every time. Median wall: K=1 6.86s → K=4 4.39 → K=8 4.89 → K=12 3.73 → K=16 3.67. So ~1.8x speedup, diminishing past K≈12 (ceiling = pod cores). 

Key proof: fan-out DOES speed up on real infra — the earlier local docker-compose run showed no gain ONLY because one kubectl port-forward serialised the concurrent sub-counts. Lesson: never benchmark a fan-out/parallel feature through a single shared local transport; measure on deployed infra or via mongo explain. Also: shared dev cluster timings are noisy (a K=1 run hit 12s vs 6s) — report medians, treat as directional. Recommendation: K 8–12 on the current dev pod.

## Related

- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]
- [[Partition the materialized count on a uniform _shard int]]
- [[not _id]]

%% ai-graph-start %%

**Related notes:**
- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]

**Relations:**
- Dev benchmark — *evaluates* — _shard count fan-out
- _shard count fan-out — *is part of* — luz-docs
- _shard count fan-out — *has ID* — LUZ-154613
- _shard count fan-out — *has date* — 2026-06-17
- Dev benchmark — *conducted with* — 128k docs tenant
- Dev benchmark — *conducted with* — deployed image
- Dev benchmark — *conducted with* — real service mesh
- Dev benchmark — *swept K value* — K=1
- Dev benchmark — *swept K value* — K=4
- Dev benchmark — *swept K value* — K=8
- Dev benchmark — *swept K value* — K=12
- Dev benchmark — *swept K value* — K=16
- K=1 — *resulted in median wall time* — 6.86s
- K=4 — *resulted in median wall time* — 4.39s
- K=8 — *resulted in median wall time* — 4.89s
- K=12 — *resulted in median wall time* — 3.73s
- K=16 — *resulted in median wall time* — 3.67s
- _shard count fan-out — *achieved* — ~1.8x speedup
- speedup — *diminishes past* — K=12
- K=12 — *is ceiling of* — pod cores
- _shard count fan-out — *speeds up on* — real infra
- local docker-compose run — *showed no gain* — _shard count fan-out
- kubectl port-forward — *serialised* — concurrent sub-counts
- local port-forward — *hid gain of* — _shard count fan-out
- Dev benchmark — *should be measured on* — deployed infra
- Dev benchmark — *should be measured via* — mongo explain
- shared dev cluster — *has property* — noisy timings
- Recommendation — *for K* — K 8–12
- K 8–12 — *applies to* — current dev pod
- Dev benchmark — *related to* — _shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup
- Dev benchmark — *related to* — Partition the materialized count on a uniform _shard int
- Dev benchmark — *related to* — not _id

%% ai-graph-end %%