---
title: "Generate a git patch under autocrlf so git apply matches a CRLF worktree"
created: 2026-08-19
type: lesson
status: seedling
source: "leo-customer360 deployments/proxy, 2026-08"
tags: [git, windows, crlf, patch, gotcha]
---

# Generate a git patch under autocrlf so git apply matches a CRLF worktree

On Windows with `core.autocrlf=true` (worktree = CRLF, index/blobs = LF), a patch you intend to ship in the repo and re-apply later must be generated with the DEFAULT git settings, NOT `-c core.autocrlf=false`. `git diff -c core.autocrlf=false` emits LF context lines; `git apply` onto a CRLF worktree then fails with "patch does not apply" (trailing `\r` mismatch). Generating with plain `git diff` produces a patch that plain `git apply` re-applies cleanly (git normalizes per autocrlf).

**How to apply:** generate `git diff -- <files> > x.patch`, revert the worktree, then verify with the exact command the consumer will run — `git apply --check x.patch`. If it still balks, `git apply --ignore-whitespace` (treats the trailing `\r` as whitespace) and `git apply --3way` are the fallbacks.

Related: [[Apostrophe inside bash ${var:?message} breaks the parser]]
