---
title: "Apply one feature from a stale branch without reverting newer work (checkout ref -- paths)"
created: 2026-07-02
type: howto
status: seedling
source: "session 2026-07-02"
tags: [git, cherry-pick, merge, workflow]
---

# Apply one feature from a stale branch without reverting newer work (checkout ref -- paths)

When you want to bring ONE feature from a feature branch onto a branch that has since diverged, do NOT `git merge` the feature branch if it is behind — the merge diff will include the branch UNDOING work that landed on the target after the fork point (e.g. `git diff main..feature` showed the Graphify install flow being *deleted*, purely because the feature branch predated that merge).

Two clean ways to apply only the feature:
1. `git cherry-pick <feature-commit>` — replays just that commit.
2. `git checkout <feature-ref> -- <paths...>` — overwrites + stages exactly those files with the branch versions.

Option 2 is exact and safe **when the target branch has NOT modified those same files since the merge base** — verify with `git log <merge-base>..<target> -- <paths>` (empty = no divergence, so branch version == merge-base + feature diff == the adapted result). It also leaves unrelated dirty files in the working tree untouched, so it is safe to run mid-work.

Applied 2026-07-02: pulled the 7 story-to-process-flow files from `origin/feature/story-to-process-flow` onto `main` (which had newer Graphify + Polaris work) without reverting anything; confirmed with `tsc --noEmit` + the feature vitest files (30 tests green).
