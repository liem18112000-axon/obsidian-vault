---
ai_hash: d78c5f6d932684ea
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities: []
source: luz-docs LUZ-154613 session 2026-06-23
status: seedling
tags:
- config
- deployment
- kubernetes
- luz-docs
- gotcha
title: Renaming a config key silently disables the feature if old env var name stays
  in overlays
type: lesson
---

# Renaming a config key silently disables the feature if old env var name stays in overlays

When a service's config key is **renamed in code** (e.g. MicroProfile `@ConfigProperty(name=...)`), any deployment overlay / system.properties still carrying the **old env-var name** is now dead config: the new code never reads it, the old value is silently ignored, and the feature **falls back to its default** — often "off" — with no error.

This is a silent, deploy-time regression: builds pass, pods start, the feature just quietly stops working. Especially dangerous for perf tuning where "off" still functions correctly, just slower.

**Rule:** when renaming a config key, grep every overlay/env file for the OLD name and rename it in lockstep with the code, across all environments.

Example: luz-docs renamed `luz.docs.materialize.count-fanout-partitions` → `luz.docs.parallelize.count-partitions` (env `LUZ_DOCS_MATERIALIZE_COUNT_FANOUT_PARTITIONS` → `LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS`). Leaving the old `...MATERIALIZE...=6` in the 7 k8s overlays would have left count fan-out at default 1 (disabled).

**Rollout-ordering corollary:** removing the old key from overlays drops the feature to default on any env whose overlay applies *before* the new image deploys — so coordinate the overlay merge with the image rollout.

See also [[MicroProfile @ConfigProperty int with blank value crashes at boot; omit the key to use defaultValue]].

## Related

- [[MicroProfile @ConfigProperty int with blank value crashes at boot; omit the key to use defaultValue]]

%% ai-graph-start %%

**Related notes:**
- [[MicroProfile @ConfigProperty int with blank value crashes at boot; omit the key to use defaultValue]]
- [[Luz K count-partitions env var]]
- [[Materialize tenant allowlist removed - cascade unconditional]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)]]

%% ai-graph-end %%