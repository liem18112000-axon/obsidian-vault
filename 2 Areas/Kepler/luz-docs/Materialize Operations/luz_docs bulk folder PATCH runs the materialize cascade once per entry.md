---
title: "luz_docs bulk folder PATCH runs the materialize cascade once per entry"
created: 2026-06-05
type: observation
status: seedling
source: "session 2026-06-05 LUZ-154804"
tags: [luz-docs, materialize, bulk-update, performance]
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
