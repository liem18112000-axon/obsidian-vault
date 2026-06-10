---
title: "MongoDB forbids $lookup inside update pipeline (WriteError 72)"
created: 2026-06-09
type: lesson
status: seedling
source: "luz-docs materialize code review 2026-06-09"
tags: [mongodb, aggregation, update-pipeline, gotcha, lookup]
---

# MongoDB forbids $lookup inside update pipeline (WriteError 72)

MongoDB **does not allow `$lookup` inside an update-with-aggregation-pipeline** (`db.coll.updateMany(filter, [ ...pipeline... ])`). The server rejects it with **WriteError code 72** (InvalidOptions / pipeline stage not allowed). Only a subset of agg stages are legal in an update pipeline (`$set`/`$addFields`, `$unset`, `$replaceRoot`/`$replaceWith`, `$project`).

**Workaround:** prefetch the joined data in application code, then **inline it into the pipeline as literal arrays** and index into them per element. Pattern: build two parallel literals — `[id0, id1, ...]` and `[[codes0], [codes1], ...]` — then inside a `$map` over the doc's own array use `$indexOfArray` to find the position and `$arrayElemAt` to pull the matching joined value.

**Trade-off:** the command body grows with the size of the prefetched data. On large joins the inlined literals can push the command document toward the **16MB command-size limit**, and every retry resends the same oversized command. Bound the prefetch (or chunk the updateMany) when the joined set is large.

## Related

- [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
