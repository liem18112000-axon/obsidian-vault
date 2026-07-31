---
ai_hash: fe0494646675d595
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: vinnstack BDD Run Tests scenario-timeout bugfix, 2026-07-12
status: seedling
tags:
- nodejs
- windows
- child-process
- gotcha
- process-management
title: Node child_process.kill on Windows doesn't kill descendant processes
type: lesson
---

# Node child_process.kill on Windows doesn't kill descendant processes

On Windows, calling `child.kill("SIGKILL")` (or any signal) on a Node `child_process` only terminates that ONE process — it does NOT cascade to that process's own children, even though POSIX process-group kills (`kill(-pid)`) do cascade on Linux/macOS. Windows has no equivalent built into `child.kill()`.

This matters a lot when the spawned process is itself a shell/wrapper that starts further processes — e.g. `spawn("bash.exe", [scriptPath])` where the script does `python -m behave ... | tee logfile`. Killing the `bash.exe` handle leaves `python.exe` and `tee.exe` running as orphans, since Windows process trees don't auto-cascade termination.

Consequence if you don't handle this: a "timeout" enforced by your own code (e.g. `setTimeout(() => child.kill(...), timeoutMs)`) doesn't actually stop the real work. It just stops your own process from listening/reporting — the orphaned child process keeps running to completion (or forever) in the background, invisible to your app, potentially still writing to shared files/logs, holding ports, or mutating shared state (e.g. a test/database), while your app has already reported a false "timeout" failure.

Fix: on Windows, use `taskkill /pid <pid> /t /f` (the `/t` flag kills the whole process tree, `/f` forces it) instead of `child.kill()`. On POSIX, `process.kill(-pid, "SIGKILL")` (negative pid targets the process GROUP) works if the child was spawned with `detached: true` to make it its own group leader — otherwise fall back to `process.kill(pid, "SIGKILL")`.

Found while debugging vinnstack's BDD "Run Tests" feature (lib/bdd/verifyRunner.ts's `runScript`): a scenario that took ~10.5 minutes was being killed by a 5-minute timeout, but the underlying `bash → python -m behave | tee` pipeline kept running anyway and wrote a full, real completed result to its own log file — completely undetected by the app, which had already reported "timed out". Related: [[A stalled-looking test step may just be a long silent poll, not a hang]] (the OTHER half of the same incident — why the timeout was too short in the first place).

## Related

- [[3 Resources/Practices/Testing/A stalled-looking test step may just be a long silent poll, not a hang]]

%% ai-graph-start %%

**Related notes:**
- [[A stalled-looking test step may just be a long silent poll, not a hang]]
- [[Windows child processes survive when only the parent is killed]]
- [[Spawning a prompting CLI hangs on open stdin — use stdio stdin ignore for EOF]]
- [[Windows claude subprocess is a process tree — taskkill T to reap it]]
- [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]]

%% ai-graph-end %%