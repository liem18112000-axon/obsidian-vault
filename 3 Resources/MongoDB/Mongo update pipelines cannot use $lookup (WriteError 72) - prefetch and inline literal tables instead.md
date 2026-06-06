---
title: "Mongo update pipelines cannot use $lookup (WriteError 72) - prefetch and inline literal tables instead"
created: 2026-06-05
type: lesson
status: budding
source: "luz-docs parent-change cascade, session 2026-06-05"
tags: [mongodb, aggregation, update-pipeline, gotcha, luz-docs]
---

# Mongo update pipelines cannot use $lookup (WriteError 72) - prefetch and inline literal tables instead

An update-with-aggregation-pipeline (`updateMany(filter, [stages])`) only accepts a restricted stage set (`$set`/`$addFields`, `$unset`, `$replaceRoot`/`$replaceWith`, `$project`); `$lookup` there fails with **WriteError 72 (cannot use expression in update pipeline)**. When a per-document update needs data from *other* documents, do the join in application code: prefetch the needed rows, then inline them into the pipeline as **two parallel literal arrays** — a key table `[k1, k2, ...]` and a value table `[v1, v2, ...]` — and resolve at runtime with `{$indexOfArray: [<keys>, <docKey>]}` → `{$arrayElemAt: [<values>, "$$j"]}`, falling back to the existing value when the index is `-1`.

Trade-off: the literal tables travel inside the command document, so very large prefetch sets bloat the command (16 MB BSON cap) — fine for bounded sets like an affected-folder subtree, wrong for unbounded joins.

Found while building the luz-docs folder parent-change cascade (the first attempt used `$lookup` and failed at runtime, not at parse time in tests).

## Related

- [[luz_docs has two materialize cascade delivery mechanisms]]
