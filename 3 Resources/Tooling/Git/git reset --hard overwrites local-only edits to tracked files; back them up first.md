---
ai_hash: b0df94893ec52e0e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: fb-info-project rollback, session 2026-06-30
status: seedling
tags:
- git
- reset
- gotcha
- safety
title: git reset --hard overwrites local-only edits to tracked files; back them up
  first
type: lesson
---

# git reset --hard overwrites local-only edits to tracked files; back them up first

`git reset --hard <sha>` moves the branch pointer AND overwrites the index AND the working tree to match <sha>. So any **uncommitted local edits to tracked files** are silently destroyed — including files you keep locally-modified on purpose and never commit (e.g. a `session/*.json` holding live auth cookies, a tweaked `run.cmd`, local config).

**Before a hard reset, back up such files outside the repo** (copy to a scratch dir), reset, then copy them back. `git stash` also works but can conflict on `pop` if the file differs at the target commit.

Real case (fb-info-project rollback to a pre-merge commit): `session/fb_session.json` (FB cookies) and `run.cmd` were working-tree-modified; a plain `git reset --hard` would have reverted the cookies to a stale committed version and logged the scraper out. Backing them up first and restoring after preserved them while the source code rolled back.

Also: a hard reset that drops commits is reversible — the dropped commits live in the reflog and on any branch/remote that still points at them (here the work stayed on `origin/feat/thorough-mode`).

## Related
[[fb-info-project]]
[[Playwright click() auto-waits the full timeout on a missing locator; probe with count() first]]

## Related

- [[fb-info-project]]

%% ai-graph-start %%

**Related notes:**
- [[A concurrent session's git stash can silently revert your in-progress edits]]
- [[Pre-staged files silently merge selective commit batches - check the index first]]
- [[FETCH_HEAD is volatile when an IDE auto-fetches]]
- [[Apply one feature from a stale branch without reverting newer work (checkout ref -- paths)]]
- [[Re-check live dependencies right before committing in a shared repo]]

%% ai-graph-end %%