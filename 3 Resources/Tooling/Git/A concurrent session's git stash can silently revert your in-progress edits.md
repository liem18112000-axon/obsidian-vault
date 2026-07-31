---
title: "A concurrent session's git stash can silently revert your in-progress edits"
created: 2026-07-09
type: lesson
status: seedling
source: "vinnstack session 2026-07-09"
tags: [git, concurrency, claude-code, multi-agent, gotcha]
---

# A concurrent session's git stash can silently revert your in-progress edits

`git stash` run by *anyone* on a shared working tree — a second agent session, the user in another terminal — sweeps up **every** uncommitted change, including edits another agent just made. An Edit tool's "success" only proves the write landed at that moment; nothing stops an external process from mutating the tree afterwards.

**The tell:** files you just edited read back as their original pre-edit content, and `git stash list` shows an unexpected `stash@{0}`. Inspect it with `git stash show -p` (do **not** pop) — it usually contains your edits mixed with the other session's unrelated work, i.e. uncoordinated concurrency, not a rejection of your work.

**Response:**
- Do **not** `git stash pop` — that re-merges two in-progress features into one tree, the exact collision the stash may have been avoiding.
- Leave the stash untouched, re-read the current on-disk state of every file the task touches, and redo the edits on top of it. String-matched edits either land cleanly or fail loudly on mismatch, so redoing is safe even if unrelated code moved around them.
- Commit only your own files by explicit name — never `git add -A` / `git add .`, which would sweep in the other session's untracked work. If one file interleaves both sessions' changes, leave it uncommitted unless asked.

## Related

- [[Consolidate duplicated hardcoded config constants into one config.ts source of truth]]
- [[Pre-staged files silently merge selective commit batches - check the index first]]
