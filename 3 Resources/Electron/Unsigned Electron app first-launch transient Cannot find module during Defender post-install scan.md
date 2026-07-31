---
title: "Unsigned Electron app first-launch: transient Cannot find module during Defender post-install scan"
created: 2026-07-15
type: lesson
status: seedling
source: "Vinnstack session 2026-07-15"
tags: [electron, windows-defender, electron-builder, nsis, gotcha, vinnstack, code-signing]
---

# Unsigned Electron app first-launch: transient Cannot find module during Defender post-install scan

On a freshly-installed **unsigned** Electron app (NSIS oneClick, ~400MB unpacked), the *very first* launch can transiently fail with `Error: Cannot find module 'next'` (or any bundled module) even though the file is physically present and complete. Windows Defender scans the just-written binaries on first execution, and while that scan holds/locks files the main process's `require()` can miss a module that resolves fine seconds later.

**Evidence it is transient, not a packaging bug:** `require.resolve('next')` and `require('next')` both succeed from the exact packaged `__dirname` under Electron's embedded Node (`ELECTRON_RUN_AS_NODE=1`), and the second and later real launches load next, hit `listening on 3001`, and show the window. cwd is irrelevant — the failing launch even had a cwd that contained node_modules/next.

**Consequence:** an NSIS `runAfterFinish: true` auto-launch fires the instant install finishes — the worst moment, mid-scan — so the user's first-ever launch may silently do nothing. Retrying (Start Menu shortcut) works. Not always a single flake: on a larger fresh install (~400MB, after adding electron-updater) it failed **2–3 consecutive launches** before the scan cleared, and the error can present two ways — `Cannot find module 'next'` (package not resolved) or `Cannot find module '.../next/dist/server/next.js' ... verify the package.json has a valid "main" entry` (package resolved, main entry momentarily unreadable). Both are the same lock, at different resolution stages. Auto-update makes this recur once per delivered version.

**Mitigations:** (1) **retry the require with backoff** — implemented in Vinnstack as `requireWithRetry("next")` (6 attempts, 400ms→2s), rethrowing non-`MODULE_NOT_FOUND`/`ENOENT` errors immediately so real bugs still surface; this rides out the scan window and fixes the silent first-launch. (2) code-sign the binary (signed exes get lighter Defender treatment and largely remove both the ~40s first-scan delay and this race) — makes the retry a no-op.

Context: Vinnstack Electron app, Electron 32.3.3 / embedded Node 20.18.1, packaged with electron-builder `asar:false`.

## Related

- [[electron-builder --win CLI flag overrides win.target in package.json]]
- [[Electron]]
