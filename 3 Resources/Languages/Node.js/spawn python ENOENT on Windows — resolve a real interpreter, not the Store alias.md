---
tags: [nodejs, windows, spawn, python, gotcha]
---

# spawn python ENOENT on Windows — resolve a real interpreter, not the Store alias

`spawn("python", args, { shell: false })` from a Node server on Windows often fails with `spawn python ENOENT` even though `python --version` works in your terminal. Two causes:

1. **Microsoft Store App-Execution-Alias**: `python` on PATH is frequently a 0-byte reparse-point stub under `…\AppData\Local\Microsoft\WindowsApps\`. It works when a shell launches it (it redirects to the Store) but `CreateProcess` (shell:false) can't execute it → ENOENT.
2. **PATH mismatch**: a long-running server process may have a narrower PATH than your interactive shell, so bare `python`/`python3` isn't found at all.

**Fix — resolve to an absolute, verified interpreter before spawning:**
- Use `where` (Windows) / `which` (POSIX) to list candidates for `py`, `python`, `python3`.
- **Skip any path under `\WindowsApps\`** (the alias stub).
- **Verify each candidate actually runs** `--version` (execFileSync, stdio ignore) before trusting it.
- On Windows prefer the **`py` launcher** with `-3` — it's the official version selector and lives at a real path (`…\Python\Launcher\py.exe`). Cache the resolved argv prefix.

Then spawn `[resolvedPath, ...prefixArgs, "-m", "venv", …]`. Real interpreter paths (including a venv's own `Scripts\python.exe` after creation) spawn fine — only the bare name / Store alias is the problem.

Observed in Vinnstack's Graphify in-app installer: `spawn python ENOENT` → resolver picked `…\Python\Launcher\py.exe -3` (3.14.0) and the venv+pip install then ran clean. Related: [[In-app provisioning of a Python-venv tool with a pollable install state]].
