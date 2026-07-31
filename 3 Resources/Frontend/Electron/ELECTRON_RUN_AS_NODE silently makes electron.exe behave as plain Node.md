---
title: "ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack desktop-packaging session 2026-07-07"
tags: [electron, windows, debugging, gotcha]
---

# ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node

If `ELECTRON_RUN_AS_NODE=1` is set in the environment, invoking `electron.exe` (directly, via `npx electron`, or via the `.bin/electron` shim) runs the target script under plain Node instead of bootstrapping the real Electron app runtime. Symptoms are confusing because they look like install/packaging problems, not an env var: `require("electron")` returns the path string instead of the `{app, BrowserWindow, ...}` API object, so `app.whenReady` is `undefined` and you get `TypeError: Cannot read properties of undefined (reading 'whenReady')`; `electron --version` prints the bundled Node.js version (e.g. `v20.18.1`) instead of the Electron version (e.g. `v32.3.3`) that the real `dist/version` file in `node_modules/electron` reports.

This variable is commonly set by tooling that itself embeds Electron (editor extension hosts, CLI harnesses) so it can spawn Node scripts through their own bundled Electron/Node without opening a window — and it leaks into any subshell that tool spawns. If you are testing an Electron app from inside such a shell, check `echo $ELECTRON_RUN_AS_NODE` first, and unset it for the test invocation (`env -u ELECTRON_RUN_AS_NODE electron.exe .`) before concluding the app itself is broken.

Related: [[Node execFile cannot launch a .cmd shim on Windows without shell:true]] and [[Windows child processes survive when only the parent is killed]] were two other environment-shaped gotchas hit while debugging the same Electron app.
