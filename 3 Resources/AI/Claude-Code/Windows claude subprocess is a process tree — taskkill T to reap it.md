---
ai_hash: 7cda18cb7510eda5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-18
entities: []
source: session 2026-06-18
status: seedling
tags:
- claude-code
- windows
- subprocess
- gotcha
title: Windows claude subprocess is a process tree — taskkill /T to reap it
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Custom Telegram-Claude bridge vs official Claude Code Remote Control]]
- [[Windows child processes survive when only the parent is killed]]
- [[Node child_process.kill on Windows doesn't kill descendant processes]]
- [[Claude Code holds an open handle on every skills folder under ~.claude]]
- [[Claude Code official remote-control surfaces (web, Remote Control, Dispatch)]]

%% ai-graph-end %%