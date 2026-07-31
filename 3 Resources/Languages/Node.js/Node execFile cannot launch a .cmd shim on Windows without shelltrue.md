---
title: "Node execFile cannot launch a .cmd shim on Windows without shell:true"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack desktop-packaging session 2026-07-07"
tags: [nodejs, windows, child-process, gotcha]
---

# Node execFile cannot launch a .cmd shim on Windows without shell:true

On Windows, `child_process.execFile("gcloud", [...])` fails (typically ENOENT, or in Electron surfaces as a swallowed rejection) because `gcloud` resolves to `gcloud.cmd`, a batch-file shim — and `execFile()` does NOT go through a shell by default, unlike `exec()`. Only shell-interpretable files (`.cmd`/`.bat`/`.ps1`) need this; a real `.exe` target works fine with plain `execFile()`.

Fix: pass `{ shell: true }` (or platform-branch it: `{ shell: process.platform === "win32" }`) to `execFile`/`spawn` when the target is a known Windows CLI shim (`gcloud`, `npm`, `git` wrapped in some installs, etc.). The same applies to any other npm-installed or SDK-installed CLI that ships as a `.cmd` on Windows.

This bit while wiring an Electron main-process ADC (Application Default Credentials) check before starting a `cloud-sql-proxy` sidecar — the check silently failed every time on Windows until `shell: true` was added, even though running the exact same `gcloud` command directly in a shell worked fine. Related: [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]].
