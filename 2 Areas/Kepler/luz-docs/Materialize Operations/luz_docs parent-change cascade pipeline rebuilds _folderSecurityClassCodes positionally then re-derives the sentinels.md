---
title: "luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels"
created: 2026-06-05
type: model
status: budding
source: "MaterializeComputeBuilder.java, session 2026-06-05"
tags: [luz-docs, materialize, mongodb, aggregation]
---

# luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels

The parent-change cascade update (built by `MaterializeComputeBuilder.buildFolderParentChangePipeline`) is a 2-stage `$addFields` pipeline run via `updateMany` on docs matching `{folderIds: {$in: [affected...]}}`:

**Stage 1 — positional rebuild of `_folderSecurityClassCodes`.** `$map` over `{$range: [0, {$size: "$folderIds"}]}` (index `i`); for each slot, `$indexOfArray` looks the folder id up in an inlined literal id table (the Java-prefetched own ∪ inherited code unions — see the literal-table technique). Hit (`j >= 0`) → take `{$arrayElemAt: [<literal unions>, "$$j"]}`; miss → keep the existing slot `{$ifNull: [{$arrayElemAt: ["$_folderSecurityClassCodes", "$$i"]}, []]}` — unaffected folders' codes are still valid because a parent-change only alters the affected subtree. `_folderNames` is deliberately untouched (parent-chain-independent).

**Stage 2 — re-derive the doc-level sentinels from stage 1's output:**
- `_effectiveSecurityClassCodes` = `$setUnion` of the doc's own `securityClassCodes` and a `$reduce`/`$setUnion` fold over `_folderSecurityClassCodes`.
- `_isPublic` = doc has no own codes **AND** (it sits in no folder **OR** `$anyElementTrue` over a `$map` testing each per-folder code set for emptiness — i.e. at least one containing folder is fully open).

Every array access is `$ifNull`-guarded to `[]`, so short/missing parallel arrays degrade to "treat as empty" rather than erroring.

## Related

- [[Mongo update pipelines cannot use $lookup (WriteError 72) - prefetch and inline literal tables instead]]
- [[luz_docs onFolderParentsChange risk profile - sync fan-out]]
- [[page-read gap]]
- [[paging races]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
