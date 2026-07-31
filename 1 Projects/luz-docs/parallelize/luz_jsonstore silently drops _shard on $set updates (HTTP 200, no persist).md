---
ai_hash: 1cdeac84a8b771eb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities:
- luz_jsonstore
- _shard
- $set updates
- HTTP 200
- PARALLEL_DOCUMENT_COUNT_SHARDING campaign
- mongo
- PATCH/update path
- luz-docs
- ParallelizeMigrationExecutor
- updateOne calls
- metadata-field model
- write-path gap
- materialize fields
- JsonStoreMongoService.updateOrRemoveMetadataFilterFields
- modified-count
- DESIGN.md
- src/main/java/ch/klara/luz/docs/parallelize
- unsharded documents
- campaign completion report
- silent no-op
- request-body
- nModified 0
- status check
- response
source: session 2026-07-15 dev tenant c1085cf9
status: seedling
tags:
- luz-docs
- jsonstore
- sharding
- gotcha
- write-path
- LUZ-156856
title: luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)
type: lesson
---

# luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)

The PARALLEL_DOCUMENT_COUNT_SHARDING campaign runs and issues correct updates, but the `_shard` field never lands in mongo because **luz_jsonstore silently drops it** on the PATCH/update path.

Evidence (dev, tenant c1085cf9, 2026-07-15):
- luz-docs sends `request-body={"$set":{"_shard":<int>}}` — the loop in ParallelizeMigrationExecutor fires hundreds of updateOne calls.
- luz_jsonstore returns `status-code=200` for every one.
- The exact PATCHed doc has NO `_shard` in mongo afterward, while `_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames` and system `_` fields ARE present.
- The same doc is PATCHed repeatedly every few seconds — each campaign run re-selects it as unsharded (`{_shard:{$exists:false}}`) and retries forever.
- Net: 6 / 200006 docs have `_shard` (those 6 written by some other/direct path).

Root cause: `_shard` is not in luz_jsonstores recognized/writable metadata-field model, so `$set:{_shard}` is a silent no-op (200, nModified 0). The materialize sentinel fields persist because they were registered in jsonstore (or backfilled directly to mongo). This is the "write-path gap".

Fix direction: allow/whitelist `_shard` in luz_jsonstores writable field set (as was done for the materialize fields), or write the shard via whatever path materialize used.

Compounding luz-docs defect: `try (var ignored = mdb.updateOne(...)) {}` discards the response entirely — no status check (the older JsonStoreMongoService.updateOrRemoveMetadataFilterFields threw on non-200). Even a status check would not catch THIS case (jsonstore returns 200); detecting it needs the modified-count. As-is, the executor reports the campaign COMPLETED while it silently no-ops and re-loops the same 200k docs.

See also the DESIGN.md in src/main/java/ch/klara/luz/docs/parallelize (shard = random int in [0, 2^30); fan-out safe only once every doc has _shard).

## Related

- [[luz-docs]]
- [[Count _shard docs per tenant via in-pod Percona mongo shell on dev]]

%% ai-graph-start %%

**Related notes:**
- [[parallelize-old-perdoc-vs-new-oneshot-in-logs]]
- [[luz-docs parallelized count undercounts documents missing _shard]]
- [[luz_docs ParallelizeGate needs two indexes on _shard]]
- [[Fan-out count needs an explicit key-absent sub-count to stay exact during shard backfill]]
- [[luz_docs stamps _shard on create to keep sharding gate stable]]

**Relations:**
- luz_jsonstore — *drops* — _shard
- luz_jsonstore — *processes* — $set updates
- luz_jsonstore — *returns* — HTTP 200
- $set updates — *do not persist* — _shard
- PARALLEL_DOCUMENT_COUNT_SHARDING campaign — *issues* — $set updates
- _shard — *is not present in* — mongo
- luz_jsonstore — *drops _shard on* — PATCH/update path
- luz-docs — *sends* — request-body
- request-body — *contains* — $set updates
- ParallelizeMigrationExecutor — *fires* — updateOne calls
- updateOne calls — *are part of* — PARALLEL_DOCUMENT_COUNT_SHARDING campaign
- _shard — *is not in* — luz_jsonstore's metadata-field model
- $set updates — *become* — silent no-op
- silent no-op — *returns* — HTTP 200
- silent no-op — *results in* — nModified 0
- write-path gap — *is the root cause* — silent no-op
- Fix direction — *is to whitelist* — _shard
- Fix direction — *is to use* — materialize fields path
- luz-docs — *has* — defect
- defect — *discards* — response
- defect — *lacks* — status check
- JsonStoreMongoService.updateOrRemoveMetadataFilterFields — *threw on* — non-200
- detecting silent no-op — *needs* — modified-count
- ParallelizeMigrationExecutor — *reports* — campaign completion report
- campaign completion report — *is misleading due to* — silent no-op
- PARALLEL_DOCUMENT_COUNT_SHARDING campaign — *re-loops* — unsharded documents
- DESIGN.md — *is located at* — src/main/java/ch/klara/luz/docs/parallelize
- luz-docs — *is related to* — this issue
- Count _shard docs per tenant via in-pod Percona mongo shell on dev — *is related to* — this issue
- _shard — *is a* — random int in [0, 2^30)
- fan-out safe — *requires* — _shard
- unsharded documents — *are selected by* — {_shard:{$exists:false}}

%% ai-graph-end %%