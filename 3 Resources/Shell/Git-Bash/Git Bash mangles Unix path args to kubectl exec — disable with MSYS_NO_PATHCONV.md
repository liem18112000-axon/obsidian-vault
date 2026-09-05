---
title: "Git Bash mangles Unix path args to kubectl exec — disable with MSYS_NO_PATHCONV"
created: 2026-08-13
type: lesson
status: seedling
source: "session 2026-08-13"
tags: [git-bash, msys2, windows, kubectl, docker, gotcha]
---

# Git Bash mangles Unix path args to kubectl exec — disable with MSYS_NO_PATHCONV

On Windows, Git Bash / MSYS2 rewrites arguments that look like absolute Unix paths into Windows paths **before** the command runs. So `kubectl exec <pod> -- df -h /tmp` sends `df -h C:/Users/.../AppData/Local/Temp` into the container, which fails with `df: No such file or directory` — even though `/tmp` exists in the container. The mangling happens on the *client* (Git Bash), not in the pod.

**Tell-tale sign:** the error message contains a Windows path you never typed (e.g. `C:/Users/<you>/AppData/Local/Temp`) where a Unix path should be.

**Fixes (prefer the scoped ones):**
- **Best:** wrap the path inside a single-quoted remote shell: `kubectl exec <pod> -- sh -c 'df -h /tmp'` — the quotes hide `/tmp` from MSYS so it passes through intact. (This is why `sh -c "du -sh /tmp"` worked in the same session while bare `df -h /tmp` did not.) Scoped to the one command, no side effects.
- `MSYS2_ARG_CONV_EXCL="*"` prefixed on the single call — excludes all args from conversion for that command only.
- `export MSYS_NO_PATHCONV=1` — disables conversion for the whole command, **but see the caveat below.**

> [!warning] `export MSYS_NO_PATHCONV=1` breaks the `gcloud` wrapper
> Do **not** set `MSYS_NO_PATHCONV=1` at script scope if the same script also calls `gcloud`. The `gcloud` shim is a bash launcher that invokes its Python entrypoint via a `/c/Users/...`-style path; with conversion disabled that path gets mangled to `C:\c\Users\...\gcloud.py` → `can't open file ... gcloud.py` → the command produces **no output** (e.g. an empty access token → downstream `401 UNAUTHENTICATED`). Observed 2026-08-13: `TOKEN=$(gcloud auth print-access-token)` silently returned empty under `MSYS_NO_PATHCONV=1`. Prefer the per-command `sh -c '...'` wrap instead of a global export, so `gcloud` keeps normal path conversion.

Applies to any CLI that forwards a literal Unix path to a remote/container context from Git Bash: kubectl exec, docker exec/run, ssh, etc. Observed 2026-08-13 while checking luz-docs-import /tmp scratch usage on GKE.

## Related

- [[Kubernetes]]
- [[kubectl]]
