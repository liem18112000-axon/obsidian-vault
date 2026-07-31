---
ai_hash: 0ddb4c135c3deae9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-09
entities: []
source: luz-docs materialize code review 2026-06-09; luz-docs parent-change cascade
  2026-06-05
status: seedling
tags:
- mongodb
- aggregation
- update-pipeline
- gotcha
- lookup
- luz-docs
title: MongoDB forbids $lookup inside update pipeline (WriteError 72)
type: lesson
---

# MongoDB forbids $lookup inside update pipeline (WriteError 72)

An update-with-aggregation-pipeline (`updateMany(filter, [stages])`) accepts only a restricted stage set — `$set`/`$addFields`, `$unset`, `$replaceRoot`/`$replaceWith`, `$project`. `$lookup` is rejected at runtime (not parse/test time) with **WriteError 72**.

**Workaround — join in application code, inline the result as literal arrays.** Prefetch the rows, then pass **two parallel literal arrays** into the pipeline: a key table `[k0, k1, …]` and a value table `[v0, v1, …]`. Resolve per element (inside a `$map` over the doc's own array) with `{$indexOfArray: [<keys>, <docKey>]}` → `{$arrayElemAt: [<values>, "$$j"]}`, falling back to the existing value when the index is `-1`.

**Trade-off:** the literal tables travel inside the command document, so the command grows with the prefetch size and every retry resends it. The 16 MB BSON/command cap is the ceiling — fine for bounded sets (an affected-folder subtree), wrong for unbounded joins; bound the prefetch or chunk the `updateMany`.

## Related

- [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
- [[luz_docs has two materialize cascade delivery mechanisms]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs bulk updateMany recompute is set-based - one event, batched literal-table pipeline, not per-doc fan-out]]
- [[luz-docs updateManyByFilter requires every targeted document to actually change]]
- [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]

%% ai-graph-end %%