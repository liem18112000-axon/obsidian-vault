---
title: "Node execFileSync needs shell:true to find .cmd-wrapped CLIs on Windows"
created: 2026-07-23
type: lesson
source: "session 2026-07-23, luz-docs earchive-perf-trace tool"
tags: [nodejs, windows, gotcha]
---

# Node execFileSync needs shell:true to find .cmd-wrapped CLIs on Windows

On Windows, many CLI tools installed via installer scripts (gcloud, npm, etc.) are actually a `.cmd` or `.bat` wrapper batch file on PATH, not a native `.exe`. Node's `child_process.execFileSync('gcloud', args)` (and `execFile`/`spawn` without `shell:true`) fails with `Error: spawnSync gcloud ENOENT` even though `gcloud` works fine when typed directly in a terminal -- Windows' CreateProcess doesn't resolve `.cmd` extensions the way a shell's PATH lookup does, so Node needs an actual shell in the loop to find it.

Fix: pass `{ shell: true }` in the options object. Cross-platform safe -- on Linux/Mac it just runs the command via `/bin/sh -c`, which still resolves `gcloud` on PATH fine, so the option doesn't need to be conditional on OS.

Caveat: with `shell:true`, argument array elements get interpolated into a shell command line rather than passed as an exec-style argv array, so avoid it for any argument containing untrusted/attacker-controlled input (shell-injection risk) -- fine here since all arguments are internally constructed (filter strings, tenant ids already regex-validated).
