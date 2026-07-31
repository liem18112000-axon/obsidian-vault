---
title: "Dev benchmark: _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain"
created: 2026-06-17
type: observation
status: seedling
source: "LUZ-154613 session 2026-06-17"
tags: [luz-docs, benchmark, performance, fanout, materialize]
---

# Dev benchmark: _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain

Dev benchmark of the luz-docs _shard count fan-out (LUZ-154613, 2026-06-17, tenant 128k docs, deployed image, real service mesh). Same query swept over K=1/4/8/12/16, count exact 128000 every time. Median wall: K=1 6.86s → K=4 4.39 → K=8 4.89 → K=12 3.73 → K=16 3.67. So ~1.8x speedup, diminishing past K≈12 (ceiling = pod cores). 

Key proof: fan-out DOES speed up on real infra — the earlier local docker-compose run showed no gain ONLY because one kubectl port-forward serialised the concurrent sub-counts. Lesson: never benchmark a fan-out/parallel feature through a single shared local transport; measure on deployed infra or via mongo explain. Also: shared dev cluster timings are noisy (a K=1 run hit 12s vs 6s) — report medians, treat as directional. Recommendation: K 8–12 on the current dev pod.

## Related

- [[_shard fan-out uses idx_shard (IXSCAN exact slice); local port-forward masks the speedup]]
- [[Partition the materialized count on a uniform _shard int]]
- [[not _id]]
