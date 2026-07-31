---
title: "Electron GPU process launch failure is fatal; disable hardware acceleration to avoid it"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [electron, chromium, gpu, gotcha, crash]
---

# Electron GPU process launch failure is fatal; disable hardware acceleration to avoid it

In Electron (Chromium under the hood), if the GPU process repeatedly fails to launch — commonly on VMs, RDP/VDI sessions, or machines with a broken, blocked, or missing GPU driver — Chromium eventually gives up and treats it as FATAL: it logs `GPU process isn't usable. Goodbye.` and the entire app process exits, not just GPU-accelerated features degrading gracefully.

The symptom in logs is a repeating `GPU process launch failed: error_code=18` from `gpu_process_host.cc`, followed by a `FATAL` line from `gpu_data_manager_impl_private.cc`. This is easy to misdiagnose as an app-level crash since the stack trace points into Chromium internals, not your own code.

The fix, when your app doesn't need GPU-accelerated rendering (most non-graphics-heavy Electron apps): call `app.disableHardwareAcceleration()` in the main process, before any other `app` API is used (before `app.whenReady()`, ideally right after requiring `electron`). This makes Chromium render everything in software mode and skips spawning the GPU process entirely, so a broken driver can never trigger the fatal path.

Applied in Vinnstack's `electron/main.js` (2026-07-07) after the packaged desktop app crashed on launch with this exact log signature.
