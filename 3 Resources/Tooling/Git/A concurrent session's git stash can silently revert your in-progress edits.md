---
title: "A concurrent session's git stash can silently revert your in-progress edits"
created: 2026-07-09
type: lesson
status: seedling
source: "vinnstack session 2026-07-09"
tags: [git, concurrency, claude-code, multi-agent, gotcha]
---

# A concurrent session's git stash can silently revert your in-progress edits

While an agent (Claude Code or otherwise) is mid-edit on a shared git working tree, another concurrent process — a second agent session, or the user themselves in another terminal — running `git stash` will sweep up EVERY uncommitted change in the tree, including edits the first agent just made and had every reason to believe were still on disk (its own Edit-tool calls had already confirmed success). The tool's prior "success" confirmation only proves the edit landed at THAT moment — it is not a guarantee the file still looks that way when read again later, because nothing prevents an external process from mutating the working tree in between.

The tell: files that were just edited successfully read back as their ORIGINAL, pre-edit content on a later read, and `git stash list` shows an unexpected `stash@{0}` entry. Diffing that stash (`git stash show -p`, WITHOUT popping it) usually reveals it contains a mix of the first agent's edits AND unrelated in-progress work from whoever ran the stash — i.e. genuinely concurrent, uncoordinated editing of the same repo, not a deliberate rejection of the agent's work.

The safe response is NOT to `git stash pop` (that would silently re-merge two different in-progress features back into the working tree at once, exactly the collision the stash may have been trying to avoid) and NOT to treat it as the user rejecting the work. Instead: leave the stash untouched, re-read the current on-disk state of every file the task touches (don't trust memory of what a prior Edit call wrote), and redo the specific edits directly on top of whatever is currently on disk. Because file edits are matched by exact string content rather than line number, redoing them is safe even when a concurrent session has since added unrelated code around the same functions — the edit either lands cleanly or fails loudly on a content mismatch, it does not silently corrupt anything.

When finishing the task, stage and commit ONLY the specific files belonging to the current task via explicit filenames (never `git add -A`/`git add .`), since the shared working tree may contain other unrelated in-progress files (new untracked files from the other session) that must not be swept into the commit. If a single shared file contains both your edit and the other session's unrelated in-progress work interleaved, the safest default is to not commit that file at all unless explicitly asked to, since committing it would bundle someone else's unfinished, unreviewed work into your commit message without their say-so.

## Related

- [[Consolidate duplicated hardcoded config constants into one config.ts source of truth]]
