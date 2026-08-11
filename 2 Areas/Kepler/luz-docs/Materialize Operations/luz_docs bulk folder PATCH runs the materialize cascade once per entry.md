---
ai_hash: f7d1432b7f8a37d3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities:
- luz_docs bulk folder PATCH
- materialize cascade
- updateManyFolderByPatch
- single-folder PATCH path
- updatePatchFolderMetadata
- processUpdatingFolderMetadataByPatch
- cascadeMaterializeSubtree
- document re-materialization cascade
- patch query
- parentFolderIds op
- N independent subtree cascades
- overlapping subtrees
- same docs repeatedly
- seen dedup
- onFolderParentsChange
- partial doc-cascade failure
- bulk response's failedEntries
- PARTIAL marker
- folder-level failures
- validation
- patch write
- security-class process
- updateSecurityClasses
- inheritedSecurityClassCode
- subtree cascade
- luz_docs materialize passive retry via cascade markers
source: session 2026-06-05 LUZ-154804
status: seedling
tags:
- luz-docs
- materialize
- bulk-update
- performance
title: luz_docs bulk folder PATCH runs the materialize cascade once per entry
type: observation
---

# luz_docs bulk folder PATCH runs the materialize cascade once per entry

`updateManyFolderByPatch` is a per-entry loop over the single-folder PATCH path (`updatePatchFolderMetadata` -> `processUpdatingFolderMetadataByPatch` -> `cascadeMaterializeSubtree`), so the document re-materialization cascade applies to bulk folder patches too — but once **per folder entry**, not once per request.

Consequences:
- All entries share one patch query; if it contains a `parentFolderIds` op, N entries = N independent subtree cascades. The `seen` dedup in `onFolderParentsChange` is per-invocation, so overlapping subtrees (parent + child both in entries) re-materialize the same docs repeatedly — idempotent $set, but multiplied cost.
- Since the passive-retry change, a partial doc-cascade failure no longer throws, so it never lands in the bulk response's `failedEntries` — the folder counts as success and the docs heal via the PARTIAL marker. Only folder-level failures (validation, the patch write, the security-class process) still mark an entry failed.
- The internal `updateSecurityClasses` -> `updateManyFolderByPatch` call patches only `inheritedSecurityClassCode` (no parentFolderIds op), so the subtree cascade does not fire on that path.

Related: [[luz_docs materialize passive retry via cascade markers]]

## Related

- [[luz_docs materialize passive retry via cascade markers]]

%% ai-graph-start %%

**Related notes:**
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]
- [[luz_docs onFolderParentsChange risk profile - sync fan-out, page-read gap, paging races]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[Folder recovery reuses the parent-change materialize cascade]]

**Relations:**
- luz_docs bulk folder PATCH — *runs* — materialize cascade
- materialize cascade — *runs* — once per entry
- updateManyFolderByPatch — *is a* — per-entry loop
- updateManyFolderByPatch — *loops over* — single-folder PATCH path
- single-folder PATCH path — *includes* — updatePatchFolderMetadata
- updatePatchFolderMetadata — *includes* — processUpdatingFolderMetadataByPatch
- processUpdatingFolderMetadataByPatch — *includes* — cascadeMaterializeSubtree
- document re-materialization cascade — *applies to* — luz_docs bulk folder PATCH
- document re-materialization cascade — *applies* — once per folder entry
- patch query — *contains* — parentFolderIds op
- parentFolderIds op — *leads to* — N independent subtree cascades
- overlapping subtrees — *re-materialize* — same docs repeatedly
- seen dedup — *is* — per-invocation
- seen dedup — *is in* — onFolderParentsChange
- partial doc-cascade failure — *no longer* — throws
- partial doc-cascade failure — *does not land in* — bulk response's failedEntries
- folder — *counts as* — success
- docs — *heal via* — PARTIAL marker
- folder-level failures — *mark* — entry failed
- folder-level failures — *include* — validation
- folder-level failures — *include* — patch write
- folder-level failures — *include* — security-class process
- updateSecurityClasses — *calls* — updateManyFolderByPatch
- updateSecurityClasses — *patches* — inheritedSecurityClassCode
- inheritedSecurityClassCode — *has* — no parentFolderIds op
- subtree cascade — *does not fire on* — that path
- luz_docs bulk folder PATCH — *related to* — luz_docs materialize passive retry via cascade markers

%% ai-graph-end %%