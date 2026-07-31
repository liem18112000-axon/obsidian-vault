---
title: "MongoDB forbids $lookup inside update pipeline (WriteError 72)"
created: 2026-06-09
type: lesson
status: seedling
source: "luz-docs materialize code review 2026-06-09; luz-docs parent-change cascade 2026-06-05"
tags: [mongodb, aggregation, update-pipeline, gotcha, lookup, luz-docs]
---

# MongoDB forbids $lookup inside update pipeline (WriteError 72)

An update-with-aggregation-pipeline (`updateMany(filter, [stages])`) accepts only a restricted stage set — `$set`/`$addFields`, `$unset`, `$replaceRoot`/`$replaceWith`, `$project`. `$lookup` is rejected at runtime (not parse/test time) with **WriteError 72**.

**Workaround — join in application code, inline the result as literal arrays.** Prefetch the rows, then pass **two parallel literal arrays** into the pipeline: a key table `[k0, k1, …]` and a value table `[v0, v1, …]`. Resolve per element (inside a `$map` over the doc's own array) with `{$indexOfArray: [<keys>, <docKey>]}` → `{$arrayElemAt: [<values>, "$$j"]}`, falling back to the existing value when the index is `-1`.

**Trade-off:** the literal tables travel inside the command document, so the command grows with the prefetch size and every retry resends it. The 16 MB BSON/command cap is the ceiling — fine for bounded sets (an affected-folder subtree), wrong for unbounded joins; bound the prefetch or chunk the `updateMany`.

## Related

- [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
