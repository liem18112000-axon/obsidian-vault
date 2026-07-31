---
ai_hash: 4d92fb56304f7dbf
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: vinnstack desktop-packaging 2026-07-07; perf tenant seeding 2026-07-23
status: seedling
tags:
- windows
- child-process
- process-management
- bash
- git-bash
- gotcha
title: Windows child processes survive when only the parent is killed
type: lesson
---

# Windows child processes survive when only the parent is killed

On Windows a spawned child is not placed in a job object tied to its parent (`child_process.spawn()` doesn't do it by default, and MSYS/Git Bash job-control signals don't cascade across the Windows boundary). So when the parent dies — crash, `taskkill` **without** `/T`, Task Manager "End task", a plain `.kill()`, or a tool reporting a backgrounded command as "killed"/"stopped" — the child keeps running, orphaned, still holding its port, file lock, or database connection. POSIX process groups usually take children down; Windows does not.

**Kill the tree, don't kill the parent:**
- Windows: `taskkill /PID <pid> /T /F` (`/T` = whole subtree), not `child.kill()`.
- Elsewhere: `process.kill(pid, "SIGKILL")` on the direct child is usually enough for one level.

**Verify before relaunching.** After any backgrounded command that spawned a subprocess reports killed, check with `ps aux` (or the platform equivalent) that the worker is actually gone, and `kill -9` it explicitly if not. This matters most for jobs that mutate shared/expensive state.

Two real bites:
- An Electron app's `stopCloudSqlProxy()` only called `.kill()`, leaving the cloud-sql-proxy sidecar and its port alive after the app was killed. (The same repo already had a `killTreeByPid` helper — grep for `taskkill` before re-deriving this.)
- A Mongo seeding script was run 5× in Git Bash, each earlier run reported "killed" after a timeout; `ps aux` showed **five** live `node` processes all still inserting into the same collection, overshooting the target by ~200k documents.

## Related

- [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]]
- [[Windows holds file handles briefly after taskkill — rmSync EPERMs, so retry]]

%% ai-graph-start %%

**Related notes:**
- [[Node child_process.kill on Windows doesn't kill descendant processes]]
- [[Windows claude subprocess is a process tree — taskkill T to reap it]]
- [[Windows holds file handles briefly after taskkill — rmSync EPERMs, so retry]]
- [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]]
- [[Node spawn shellfalse on Windows won't run .cmd.ps1 wrappers (ENOENT)]]

%% ai-graph-end %%