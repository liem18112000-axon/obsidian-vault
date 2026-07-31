---
title: "Shard count fan-out: most of the win is at K=4, diminishing returns after"
created: 2026-06-17
type: observation
status: seedling
source: "luz_docs LUZ-154613 DEV sweep 2026-06-17"
tags: [performance, mongodb, luz-docs, benchmarking]
---

# Shard count fan-out: most of the win is at K=4, diminishing returns after

Empirical DEV sweep of the luz-docs `_shard` parallel-count fan-out (LUZ-154613), tenant with 128k matching docs, 10 sequential samples per K, `_shard` indexed + fully stamped:

| K | median latency | vs K=1 |
|---|---|---|
| 1 (off) | 14.4 s | 1.0x |
| 4 | 4.3 s | 3.3x |
| 8 | 4.2 s | 3.5x |
| 12 | 3.4 s | 4.3x |
| 16 | 3.5 s | 4.1x |

Takeaways:
- **Most of the speedup lands at K=4** (14.4 -> 4.3 s). K=8/12/16 only shave another ~1 s while spiking CPU more (transient bursts up to ~3 cores, brushing the 3-core pod limit at K=4/12). RAM flat (~1 Gi) regardless of K.
- Knee is around K=4-8; the deployed default of 16 is past it. K=8 is a balanced default; K=4 if Mongo/connection-pool load matters more than the last ~0.8 s.
- Count was exact (128000) at every K -> the no-shard-missing-bucket design is safe ONLY because every doc was stamped first (see [[3 Resources/Data/MongoDB/Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]).
- Measured sequentially (1 request at a time). Under concurrency, K x concurrency = simultaneous Mongo sub-counts, so re-measure before fixing a prod default.

Method gotcha: K is a server config (`LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS`), not a request param — sweeping it means patch configmap + rollout restart per K.

## Related

- [[3 Resources/Data/MongoDB/Count fan-out _shard index must put _shard LAST in the compound key (ESR)]]
- [[Widening fan-out threads doesn't help once MongoDB is the count bottleneck]]
- [[BitmapHLL counts supersede fan-out; they don't combine with it]]
