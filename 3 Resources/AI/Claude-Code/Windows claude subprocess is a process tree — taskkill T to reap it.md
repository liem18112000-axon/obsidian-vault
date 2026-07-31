---
title: "Windows claude subprocess is a process tree — taskkill /T to reap it"
created: 2026-06-18
type: lesson
status: seedling
source: "session 2026-06-18"
tags: [claude-code, windows, subprocess, gotcha]
---

# Windows claude subprocess is a process tree — taskkill /T to reap it

On Windows, Claude Code is launched as `claude.CMD` (a batch wrapper). Spawning it from Python needs `cmd.exe /c claude.CMD ...` (CreateProcess refuses to run `.CMD`/`.BAT` directly). The result is a **process tree**: `cmd.exe` → `node`.

`subprocess.Popen.terminate()` (and `taskkill /PID` without `/T`) only kills the `cmd.exe` wrapper, leaving the `node` child — the actual claude run — **orphaned and still running**.

Kill the whole tree instead:

```python
subprocess.run(["taskkill", "/F", "/T", "/PID", str(proc.pid)])  # /T = tree
```

On POSIX, `proc.terminate()` then `proc.kill()` after a grace period is enough.

Discovered building `/cancel` for the [[Custom Telegram-Claude bridge vs official Claude Code Remote Control]].

## Related

- [[Custom Telegram-Claude bridge vs official Claude Code Remote Control]]
