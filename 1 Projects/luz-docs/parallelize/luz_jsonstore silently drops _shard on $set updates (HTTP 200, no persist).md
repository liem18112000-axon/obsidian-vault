---
title: "luz_jsonstore silently drops _shard on $set updates (HTTP 200, no persist)"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15 dev tenant c1085cf9"
tags: [luz-docs, jsonstore, sharding, gotcha, write-path, LUZ-156856]
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
