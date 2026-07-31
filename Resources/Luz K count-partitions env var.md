---
title: Luz K count-partitions env var
tags: [luz-docs, config, parallelize, microprofile]
created: 2026-07-16
---

# Luz K count-partitions env var

The parallelize count fan-out factor **K** is the property `luz.docs.parallelize.count-partitions` (class `ParallelizeConstants.CONFIG_COUNT_FANOUT_PARTITIONS`).

- Default `1` (`<=1` disables fan-out). Clamped to `COUNT_PARTITIONS_MAX = 64`.
- **Env-var form** (MicroProfile Config mapping — uppercase, dots+dashes → `_`): `LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS`.
- **Change needs a pod restart** — read once at startup. `kubectl set env statefulset/luz-docs LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS=<K> -c luz-docs` triggers the rolling restart automatically.

Do NOT confuse with the materialize path's own knob `LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS` (`luz.docs.materialize.count-fanout-partitions`, default 1, clamp 64) — separate fan-out, separate config. Both can be set at once.

Benchmark context (dev, disk-bound caveat): K=8 was ~6x cold-path only while data fit the mongo cache; past ~840k docs it goes disk-bound and K stops helping. Perf run 2026-07-16 tests K=12 on the 800k tenant.

Related: [[Luz performance env cluster topology]]
