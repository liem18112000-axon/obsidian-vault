---
ai_hash: 6d5a3f82a0de4d63
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
tags:
- luz-docs
- config
- parallelize
- microprofile
title: Luz K count-partitions env var
---

# Luz K count-partitions env var

The parallelize count fan-out factor **K** is the property `luz.docs.parallelize.count-partitions` (class `ParallelizeConstants.CONFIG_COUNT_FANOUT_PARTITIONS`).

- Default `1` (`<=1` disables fan-out). Clamped to `COUNT_PARTITIONS_MAX = 64`.
- **Env-var form** (MicroProfile Config mapping — uppercase, dots+dashes → `_`): `LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS`.
- **Change needs a pod restart** — read once at startup. `kubectl set env statefulset/luz-docs LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS=<K> -c luz-docs` triggers the rolling restart automatically.

Do NOT confuse with the materialize path's own knob `LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS` (`luz.docs.materialize.count-fanout-partitions`, default 1, clamp 64) — separate fan-out, separate config. Both can be set at once.

Benchmark context (dev, disk-bound caveat): K=8 was ~6x cold-path only while data fit the mongo cache; past ~840k docs it goes disk-bound and K stops helping. Perf run 2026-07-16 tests K=12 on the 800k tenant.

Related: [[Luz performance env cluster topology]]

%% ai-graph-start %%

**Related notes:**
- [[Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[Dev benchmark _shard count fan-out ~1.8x, diminishing past K=12; local port-forward hid the gain]]
- [[eArchive 800k bottleneck is view-controller not K]]
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]
- [[Levers to optimise the visible-document count beyond _shard fan-out]]

%% ai-graph-end %%