---
title: "ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node"
aliases: ["ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken' — check/clear it before testing"]
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack desktop-packaging sessions 2026-07-07 / 2026-07-15"
tags: [electron, windows, testing, env, debugging, gotcha]
---

# ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node

If `ELECTRON_RUN_AS_NODE=1` is in the environment, `electron.exe` (directly, via `npx electron`, via the `.bin/electron` shim, or as a **packaged app exe**) runs its entry script under plain Node instead of bootstrapping the Electron runtime. `require("electron")` then returns the path string instead of the `{app, BrowserWindow, …}` object, so the app dies at its first `app.*` call — **before any logging** — and looks like a broken build.

Tells:
- `TypeError: Cannot read properties of undefined (reading 'whenReady')` / `(reading 'disableHardwareAcceleration')`.
- `electron --version` prints the bundled Node version (`v20.18.1`) instead of the Electron version (`v32.3.3` per `node_modules/electron/dist/version`).
- A GUI exe exits in seconds with no window and no startup log.

The var is commonly set by tooling that itself embeds Electron (editor extension hosts, CLI/agent harnesses) so it can spawn Node scripts through its bundled runtime without opening a window — and it leaks into every subshell that tool spawns. It cost multiple debugging cycles of a false "the packaged exe doesn't work" conclusion.

**Check first when testing any Electron app from automation:**
- `echo $ELECTRON_RUN_AS_NODE`, and launch with `env -u ELECTRON_RUN_AS_NODE …`.
- Confirm it isn't persistent: `[Environment]::GetEnvironmentVariable('ELECTRON_RUN_AS_NODE','User'/'Machine')`. If it's only in the tool session, real users double-clicking are unaffected.
- For a portable build, extract with `7za` and run the **inner** electron binary — the self-extractor detaches and does not forward ad-hoc env.

Related: [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]] (that note's original "no desktop" diagnosis was wrong — it was this env var), [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]], [[Node execFile cannot launch a .cmd shim on Windows without shell:true]], [[Windows child processes survive when only the parent is killed]].
