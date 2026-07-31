---
title: "eArchive PRs in luz_docs target earchive-master integration branch, not master"
created: 2026-06-04
type: observation
status: seedling
source: "session 2026-06-04"
tags: [luz-docs, earchive, bitbucket, git-workflow]
---

# eArchive PRs in luz_docs target earchive-master integration branch, not master

PRs for eArchive feature branches in the luz_docs repo merge into the long-lived integration branch `kepler/sprint-156/earchive-master`, **not** directly into `master`. Bitbucket auto-suggests the PR target on push (e.g. PR #1324 for branch `kepler/sprint-158/LUZ-155136-...`: https://bitbucket.org/axonivy-prod/luz_docs/pull-requests/1324).

Why it matters: when shipping or reviewing eArchive work, diff against `earchive-master` — comparing against `master` shows the whole accumulated eArchive delta, not just the branch's change.
