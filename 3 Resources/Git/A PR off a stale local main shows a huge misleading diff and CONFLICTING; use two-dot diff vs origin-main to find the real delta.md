---
title: "A PR off a stale local main shows a huge misleading diff and CONFLICTING; use two-dot diff vs origin-main to find the real delta"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20"
tags: [git, github, pull-request, merge-base, gotcha, leo-customer360]
---

# A PR off a stale local main shows a huge misleading diff and CONFLICTING; use two-dot diff vs origin-main to find the real delta

A PR branch created off a **stale local `main`** shows a **huge, misleading diff and a CONFLICTING** mergeable status, even when the real change is tiny.

**What happened (leo-customer360):** local `main` was ~30 commits behind `origin/main` (which had merged `infras/deploy/v1` via PR #2). A branch cut from local `main` produced a PR of **307 files / +48702** and GitHub reported `CONFLICTING`. The *actual* tree delta vs `origin/main` was **6 files / +115/-3**.

**Why:** GitHubs PR "Files changed" is a **three-dot** diff (`base...head`) computed from the **merge-base**. If you branched off a stale base, the merge-base is that stale commit, so the diff replays everything `origin/main` gained since — most of it already merged — and the reverse-application conflicts.

**Diagnose the real delta with a two-dot diff:**
```
git fetch origin
git diff --stat origin/main..feature-branch          # true tip-to-tip tree delta
git merge-base feature-branch origin/main            # if this == stale main, thats the smell
git log --oneline main..origin/main                  # what local main is missing
```

**Fix:** rebuild the branch on the *real* base and carry only the genuine delta:
```
OLD=$(git rev-parse feature-branch)                  # preserve current tip
git checkout -B feature-branch origin/main
git checkout "$OLD" -- <only-the-N-changed-files>
git commit && git push --force-with-lease origin feature-branch
```
The open PR updates in place; `CONFLICTING` becomes `MERGEABLE`.

**Prevention:** always `git fetch` and branch off `origin/main` (or verify `git rev-parse main` == `git rev-parse origin/main`) before starting a feature branch.

## Related

- [[New .sh CI runners must be git-staged with --chmod=+x on Windows or the [ -x ] gate skips them]]
