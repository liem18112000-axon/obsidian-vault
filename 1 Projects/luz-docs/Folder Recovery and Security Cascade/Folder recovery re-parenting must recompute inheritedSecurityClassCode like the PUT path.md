---
title: "Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path"
created: 2026-06-04
type: lesson
status: seedling
source: "LUZ-155107 / LUZ-155136, session 2026-06-04"
tags: [luz-docs, security-class, folder-recovery, gotcha]
---

# Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path

In luz_docs, recovering a folder into a *different* parent (`restoreToNewParentFolderIds` in `FolderService`) is effectively a re-parenting operation, so it must recompute `inheritedSecurityClassCode` for the folder **and its whole subtree** — exactly what the PUT metadata-update path does. The fix (LUZ-155107, refined in LUZ-155136) reuses `PutUpdatingSecurityClassFolderProcess` after persisting the new `parentFolderIds`, instead of leaving the stale inherited code from the pre-deletion parent.

Gotcha this prevents: a folder deleted under a restricted parent and recovered under a public one (or vice versa) would otherwise keep the old inherited security class, silently mis-securing every document/subfolder beneath it.

## Related

- [[eArchive PRs in luz_docs target earchive-master integration branch]]
- [[not master]]
