---
ai_hash: 9d6462a287c20eba
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- eArchive PRs
- luz_docs
- earchive-master
- master
- Bitbucket
- PR target
- eArchive feature branches
- kepler/sprint-156/earchive-master
- 'PR #1324'
- kepler/sprint-158/LUZ-155136-...
- eArchive work
- eArchive delta
- long-lived integration branch
source: session 2026-06-04
status: seedling
tags:
- luz-docs
- earchive
- bitbucket
- git-workflow
title: eArchive PRs in luz_docs target earchive-master integration branch, not master
type: observation
---

# eArchive PRs in luz_docs target earchive-master integration branch, not master

PRs for eArchive feature branches in the luz_docs repo merge into the long-lived integration branch `kepler/sprint-156/earchive-master`, **not** directly into `master`. Bitbucket auto-suggests the PR target on push (e.g. PR #1324 for branch `kepler/sprint-158/LUZ-155136-...`: https://bitbucket.org/axonivy-prod/luz_docs/pull-requests/1324).

Why it matters: when shipping or reviewing eArchive work, diff against `earchive-master` — comparing against `master` shows the whole accumulated eArchive delta, not just the branch's change.

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_integration_test AI pipeline branch and PR mechanics]]
- [[Folder-deletion batching lost to materialise cascade in sprint-158 merge]]
- [[luz_docs parent-change cascade recovers forward, not via snapshot rollback]]
- [[LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master]]
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]

**Relations:**
- eArchive PRs — *are in* — luz_docs
- eArchive PRs — *target* — earchive-master
- eArchive PRs — *do not target* — master
- eArchive feature branches — *are in* — luz_docs
- eArchive feature branches — *merge into* — kepler/sprint-156/earchive-master
- kepler/sprint-156/earchive-master — *is a* — long-lived integration branch
- Bitbucket — *auto-suggests* — PR target
- PR #1324 — *is for* — kepler/sprint-158/LUZ-155136-...
- eArchive work — *diff against* — earchive-master
- comparing against — *shows* — eArchive delta

%% ai-graph-end %%