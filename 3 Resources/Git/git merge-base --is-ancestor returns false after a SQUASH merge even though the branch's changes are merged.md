---
title: "git merge-base --is-ancestor returns false after a SQUASH merge even though the branch's changes are merged"
created: 2026-08-23
type: gotcha
tags: [git, squash-merge, branches, gotcha]
---

# git merge-base --is-ancestor returns false after a SQUASH merge even though the branch's changes are merged

After a branch is **squash-merged** (GitHub "Squash and merge"), `git merge-base --is-ancestor <branch-tip> origin/main` returns FALSE (exit 1) — because a squash merge creates a brand-NEW single commit on main containing all the changes, and does NOT record the original branch commits as ancestors. So the branch's content IS in main, but its commit SHAs are not in main's history.

Don't conclude "the work isn't merged" from an ancestry check alone. Instead:
- Look at `git log --oneline origin/main` for the squash commit — it's usually titled with the PR title + "(#N)".
- Or diff the actual FILE CONTENT (`git show origin/main:path | grep <marker>`), which is the ground truth.

Corollary: a new branch cut from the post-squash main will CONTAIN all the squashed work (via main) while showing only its own new commits in `git log origin/main..HEAD`. That's expected, not a lost-work situation.

Encountered: leo-customer360 — PR #18 (branch infras/cicd/v3) squash-merged to main as commit 5ec85fe "...(#18)"; a follow-up branch showed 2 commits over main yet its files contained all of #18's changes. 2026-08-23.
