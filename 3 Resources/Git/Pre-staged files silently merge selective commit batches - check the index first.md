---
title: "Pre-staged files silently merge selective commit batches - check the index first"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23"
tags: [git, staging, gotcha]
---

# Pre-staged files silently merge selective commit batches - check the index first

When splitting working-tree changes into multiple logical commits with selective `git add` lists, files ALREADY in the index (staged earlier by an IDE, an aborted add, or a teammate's tooling) ride along with the FIRST commit regardless of your add list — `git commit` commits the whole index, not just what you added this round. Symptoms: the second commit reports "nothing added to commit", and excluded paths (e.g. a doc/ file) appear in the pushed commit.

Prevention: before a selective-commit sequence, read the FIRST status column (`git status -s` — staged flag) and `git reset` anything unexpected, or start with `git reset` to unstage everything and build each commit's index from scratch.

Repair after pushing (without force-push): `git show <sha>:<file> > tmp` → `git checkout <sha>~1 -- <file>` → commit the reversal → copy tmp back over the file, leaving the change as an ordinary uncommitted working-tree edit.
