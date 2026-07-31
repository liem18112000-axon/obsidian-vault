---
title: "ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken' — check/clear it before testing"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15"
tags: [electron, testing, gotcha, env, debugging]
---

# ELECTRON_RUN_AS_NODE=1 in the env makes an Electron exe run as Node and 'look broken' — check/clear it before testing

Major debugging lesson (Vinnstack exe, 2026-07-15): I spent many cycles concluding "the packaged Electron exe doesn't work" — EVERY launch failed to serve and wrote no startup log. Root cause of the FALSE conclusion: the Claude Code tool session had ELECTRON_RUN_AS_NODE=1 in its process env, inherited by everything I spawned. That env var makes the Electron binary run its entry script as plain Node.js, so `require("electron").app` is undefined and the app crashes at the first `app.*` call — BEFORE any logging — looking exactly like "the exe is broken."

Tells I missed for too long: `electron script.js` giving "Cannot read properties of undefined (reading 'disableHardwareAcceleration')"/app undefined; a GUI exe exiting in <12s with no window and no log. FIX/CHECK FIRST when testing any Electron app from automation: `echo $ELECTRON_RUN_AS_NODE`, and launch with `env -u ELECTRON_RUN_AS_NODE`. Also confirm it's not persistent: [Environment]::GetEnvironmentVariable('ELECTRON_RUN_AS_NODE','User'/'Machine'). Here it was only in the tool session, NOT User/Machine — so real users (double-clicking) are unaffected.

Verification that finally worked: extract the portable exe with 7za, run the INNER electron binary directly (not the self-extractor, which detaches + doesn't forward ad-hoc env) via `env -u ELECTRON_RUN_AS_NODE ...\Vinnstack.exe` → it served HTTP 200 and the startup log showed the full happy path. 

Separately confirmed-real bug (the actual fix): cross-building the Windows exe on Linux shipped only the Linux @next/swc; without @next/swc-win32-x64-msvc the app fails at next.prepare() on Windows. That one would break for real users; ELECTRON_RUN_AS_NODE only broke MY testing. Related: [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]], [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]] (that earlier note was WRONG about the cause — it was ELECTRON_RUN_AS_NODE, not lack of a desktop).
