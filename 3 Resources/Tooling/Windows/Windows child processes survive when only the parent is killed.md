---
title: "Windows child processes survive when only the parent is killed"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack desktop-packaging session 2026-07-07"
tags: [windows, child-process, gotcha, process-management]
---

# Windows child processes survive when only the parent is killed

On Windows, `child_process.spawn()` does not put a child process in a job object tied to its parent by default. So if the parent is killed (crash, forced `taskkill` without `/T`, Task Manager "End task", a plain `.kill()` call) the child keeps running, orphaned, still holding whatever resources it had (a listening port, a file lock, etc.). This is unlike POSIX process groups, where killing a parent commonly takes its children down too (though even there it is not automatic without setsid/process-group signaling).

Fix: to reliably kill a spawned process AND its descendants on Windows, use `taskkill /PID <pid> /T /F` (the `/T` flag kills the whole subtree) instead of `child.kill()`. On other platforms, `process.kill(pid, "SIGKILL")` on the direct child is usually sufficient for a simple one-level child.

Concretely: an Electron app spawning a cloud-sql-proxy sidecar to tunnel to a Cloud SQL database left the proxy running (and the port it held) after the Electron app itself was killed, because stopCloudSqlProxy() only called .kill(). The same codebase already had this exact pattern solved elsewhere for a different spawned CLI (a killTreeByPid helper in lib/ultracodeProcs.ts, used for a spawned claude CLI process) - worth grepping for taskkill in a codebase before re-deriving this from scratch. Related: [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]].
