---
title: "Trim a cross-built Electron exe: drop the host-platform native binaries the target never uses (+ maxCompression, one locale)"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15"
tags: [electron-builder, optimization, cross-build, size, native-deps]
---

# Trim a cross-built Electron exe: drop the host-platform native binaries the target never uses (+ maxCompression, one locale)

Optimizing a cross-built Electron+Next Windows exe (Vinnstack, 2026-07-15) — config-only, no app code:
1) BIGGEST WIN: exclude the HOST-platform native binaries that the cross-build pulled in but the target never uses. Building the Windows exe on Linux, `npm ci` installed @next/swc-linux-x64-gnu (131MB) + swc-linux-x64-musl (156MB) — ~287MB of dead weight shipped inside a Windows app. Drop them via electron-builder `files`: "!node_modules/@next/swc-linux-*/**" (keep the force-installed @next/swc-win32-x64-msvc). Same idea for any platform-forked native dep (esbuild, sharp, better-sqlite3…).
2) `"compression": "maximum"` — smaller portable exe (costs build CPU/time only).
3) `"electronLanguages": ["en-US"]` — ship only the locales you use; drops the rest of Electron's ~50 locale .pak files.

These are the highest-impact, lowest-risk EXE optimizations when asar:false is required (which bloats + slows because node_modules ships loose). Verify after: extract the exe and confirm the win32 binary is still present and the linux ones are gone, and that it still opens on a clean-machine sim. Related: [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]].
