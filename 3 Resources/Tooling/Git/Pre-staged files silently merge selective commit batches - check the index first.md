---
ai_hash: da862f144480ee7e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23
status: seedling
tags:
- git
- staging
- gotcha
title: Pre-staged files silently merge selective commit batches - check the index
  first
type: lesson
---

# Pre-staged files silently merge selective commit batches - check the index first

When splitting working-tree changes into multiple logical commits with selective `git add` lists, files ALREADY in the index (staged earlier by an IDE, an aborted add, or a teammate's tooling) ride along with the FIRST commit regardless of your add list — `git commit` commits the whole index, not just what you added this round. Symptoms: the second commit reports "nothing added to commit", and excluded paths (e.g. a doc/ file) appear in the pushed commit.

Prevention: before a selective-commit sequence, read the FIRST status column (`git status -s` — staged flag) and `git reset` anything unexpected, or start with `git reset` to unstage everything and build each commit's index from scratch.

Repair after pushing (without force-push): `git show <sha>:<file> > tmp` → `git checkout <sha>~1 -- <file>` → commit the reversal → copy tmp back over the file, leaving the change as an ordinary uncommitted working-tree edit.

%% ai-graph-start %%

**Related notes:**
- [[A concurrent session's git stash can silently revert your in-progress edits]]
- [[Split intermixed single-file changes into two commits via backup and intermediate edit]]
- [[git reset --hard overwrites local-only edits to tracked files; back them up first]]
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]
- [[Command-scanning git-commit hooks miss flag-separated forms and -F message files]]

%% ai-graph-end %%