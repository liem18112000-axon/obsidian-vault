---
title: "Git Bash mangles gh api leading-slash paths to C:/... — set MSYS_NO_PATHCONV=1"
created: 2026-08-23
type: gotcha
status: seedling
source: "session 2026-08-23"
tags: [git-bash, msys, windows, gh-cli, gotcha]
---

# Git Bash mangles gh api leading-slash paths to C:/... — set MSYS_NO_PATHCONV=1

On Windows **Git Bash / MSYS**, `gh api` calls with a leading-slash path get mangled by MSYS path conversion — the arg `/repos/OWNER/REPO/...` is rewritten to a filesystem path like `C:/Program Files/Git/repos/...`, and gh fails: `invalid API endpoint: "C:/Program Files/Git/repos/...". Your shell might be rewriting URL paths as filesystem paths.`

It's intermittent — a bare `gh api "/repos/..."` may work, but the SAME call inside command substitution `x=$(gh api "/repos/...")` triggers the rewrite.

**Fixes (any):**
- `export MSYS_NO_PATHCONV=1` (or prefix the command) — disables the conversion for that call/session. Most reliable.
- Omit the leading slash: `gh api "repos/OWNER/REPO/..."` (gh's own error message suggests this).
- `MSYS2_ARG_CONV_EXCL='*'` also works.

Same class of bug hits any tool taking URL-ish `/path` args under Git Bash (curl to unix sockets, docker, kubectl exec paths). When a Windows tool complains an API path became `C:/...`, reach for MSYS_NO_PATHCONV=1.

Source: session 2026-08-23, tracing GitHub Actions runs with gh api on Windows.
