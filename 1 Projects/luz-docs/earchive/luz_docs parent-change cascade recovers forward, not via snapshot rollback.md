---
ai_hash: 45a5d40214caea14
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- luz_docs
- parent-change cascade
- eArchive materialise work
- snapshot rollback
- materializeCascadeSnapshot
- snapshotMaterializeStateForFolders
- MaterializeRepository
- SNAPSHOT_*
- MATERIALIZE_PROJECTION
- 92d527ce3
- forward recovery
- PARTIAL retry marker
- parentChangeFolderIds
- sweeper
- MaterializeRepositoryTest
- wildcard-to-explicit import cleanup
source: git 92d527ce3, session 2026-06-11
status: seedling
tags:
- luz-docs
- earchive
- materialize
- design-decision
title: luz_docs parent-change cascade recovers forward, not via snapshot rollback
type: observation
---

# luz_docs parent-change cascade recovers forward, not via snapshot rollback

Design decision in luz_docs eArchive materialise work: the folder parent-change cascade originally took a *snapshot* of document materialise state (`materializeCascadeSnapshot` collection, `snapshotMaterializeStateForFolders` in `MaterializeRepository`, `SNAPSHOT_*`/`MATERIALIZE_PROJECTION` constants) so a failed cascade could roll back. Commit `92d527ce3` ("Refactor code for forward-recover instead of snapshot", 2026-06-09, branch kepler/sprint-156/earchive-master) removed all of that in favour of **forward recovery**: a failed batch writes a PARTIAL retry marker carrying `parentChangeFolderIds`, and a sweeper re-runs the self-narrowing batch until it applies cleanly.

Gotcha from the same refactor: it swept the main code and two test classes but left two stale `snapshotMaterializeStateForFolders` tests in `MaterializeRepositoryTest`, breaking test compile later. When deleting a feature, grep the whole repo (tests included) for its symbols before committing.

## Related

- [[Verify wildcard-to-explicit import cleanup by compiling]]

%% ai-graph-start %%

**Related notes:**
- [[Folder-deletion batching lost to materialise cascade in sprint-158 merge]]
- [[LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[luz_docs materialize passive retry via cascade markers]]
- [[Snapshot for rollback must live outside retry boundary]]

**Relations:**
- parent-change cascade — *is design decision in* — luz_docs eArchive materialise work
- parent-change cascade — *originally used* — snapshot rollback
- snapshot rollback — *involved* — materializeCascadeSnapshot
- snapshot rollback — *involved* — snapshotMaterializeStateForFolders
- snapshotMaterializeStateForFolders — *is part of* — MaterializeRepository
- snapshot rollback — *used constants* — SNAPSHOT_*
- snapshot rollback — *used constants* — MATERIALIZE_PROJECTION
- 92d527ce3 — *removed* — snapshot rollback
- 92d527ce3 — *introduced* — forward recovery
- forward recovery — *uses* — PARTIAL retry marker
- PARTIAL retry marker — *carries* — parentChangeFolderIds
- forward recovery — *employs* — sweeper
- 92d527ce3 — *caused issue in* — MaterializeRepositoryTest
- MaterializeRepositoryTest — *contained tests for* — snapshotMaterializeStateForFolders
- luz_docs — *is related to* — wildcard-to-explicit import cleanup

%% ai-graph-end %%