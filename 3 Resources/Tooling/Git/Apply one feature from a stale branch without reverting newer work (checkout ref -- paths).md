---
ai_hash: 058cf2c8ba11a655
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-02
entities: []
source: session 2026-07-02
status: seedling
tags:
- git
- cherry-pick
- merge
- workflow
title: Apply one feature from a stale branch without reverting newer work (checkout
  ref -- paths)
type: howto
---

# Apply one feature from a stale branch without reverting newer work (checkout ref -- paths)

When you want to bring ONE feature from a feature branch onto a branch that has since diverged, do NOT `git merge` the feature branch if it is behind — the merge diff will include the branch UNDOING work that landed on the target after the fork point (e.g. `git diff main..feature` showed the Graphify install flow being *deleted*, purely because the feature branch predated that merge).

Two clean ways to apply only the feature:
1. `git cherry-pick <feature-commit>` — replays just that commit.
2. `git checkout <feature-ref> -- <paths...>` — overwrites + stages exactly those files with the branch versions.

Option 2 is exact and safe **when the target branch has NOT modified those same files since the merge base** — verify with `git log <merge-base>..<target> -- <paths>` (empty = no divergence, so branch version == merge-base + feature diff == the adapted result). It also leaves unrelated dirty files in the working tree untouched, so it is safe to run mid-work.

Applied 2026-07-02: pulled the 7 story-to-process-flow files from `origin/feature/story-to-process-flow` onto `main` (which had newer Graphify + Polaris work) without reverting anything; confirmed with `tsc --noEmit` + the feature vitest files (30 tests green).

%% ai-graph-start %%

**Related notes:**
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]
- [[A concurrent session's git stash can silently revert your in-progress edits]]
- [[Split intermixed single-file changes into two commits via backup and intermediate edit]]
- [[FETCH_HEAD is volatile when an IDE auto-fetches]]
- [[Pre-staged files silently merge selective commit batches - check the index first]]

%% ai-graph-end %%