---
title: "luz_docs parent-change cascade recovers forward, not via snapshot rollback"
created: 2026-06-11
type: observation
status: seedling
source: "git 92d527ce3, session 2026-06-11"
tags: [luz-docs, earchive, materialize, design-decision]
---

# luz_docs parent-change cascade recovers forward, not via snapshot rollback

Design decision in luz_docs eArchive materialise work: the folder parent-change cascade originally took a *snapshot* of document materialise state (`materializeCascadeSnapshot` collection, `snapshotMaterializeStateForFolders` in `MaterializeRepository`, `SNAPSHOT_*`/`MATERIALIZE_PROJECTION` constants) so a failed cascade could roll back. Commit `92d527ce3` ("Refactor code for forward-recover instead of snapshot", 2026-06-09, branch kepler/sprint-156/earchive-master) removed all of that in favour of **forward recovery**: a failed batch writes a PARTIAL retry marker carrying `parentChangeFolderIds`, and a sweeper re-runs the self-narrowing batch until it applies cleanly.

Gotcha from the same refactor: it swept the main code and two test classes but left two stale `snapshotMaterializeStateForFolders` tests in `MaterializeRepositoryTest`, breaking test compile later. When deleting a feature, grep the whole repo (tests included) for its symbols before committing.

## Related

- [[Verify wildcard-to-explicit import cleanup by compiling]]
