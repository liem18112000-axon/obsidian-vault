---
ai_hash: 46932716c7d9ee9f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-15
status: seedling
tags:
- electron-builder
- optimization
- cross-build
- size
- native-deps
title: 'Trim a cross-built Electron exe: drop the host-platform native binaries the
  target never uses (+ maxCompression, one locale)'
type: lesson
---

# Trim a cross-built Electron exe: drop the host-platform native binaries the target never uses (+ maxCompression, one locale)

Optimizing a cross-built Electron+Next Windows exe (Vinnstack, 2026-07-15) — config-only, no app code:
1) BIGGEST WIN: exclude the HOST-platform native binaries that the cross-build pulled in but the target never uses. Building the Windows exe on Linux, `npm ci` installed @next/swc-linux-x64-gnu (131MB) + swc-linux-x64-musl (156MB) — ~287MB of dead weight shipped inside a Windows app. Drop them via electron-builder `files`: "!node_modules/@next/swc-linux-*/**" (keep the force-installed @next/swc-win32-x64-msvc). Same idea for any platform-forked native dep (esbuild, sharp, better-sqlite3…).
2) `"compression": "maximum"` — smaller portable exe (costs build CPU/time only).
3) `"electronLanguages": ["en-US"]` — ship only the locales you use; drops the rest of Electron's ~50 locale .pak files.

These are the highest-impact, lowest-risk EXE optimizations when asar:false is required (which bloats + slows because node_modules ships loose). Verify after: extract the exe and confirm the win32 binary is still present and the linux ones are gone, and that it still opens on a clean-machine sim. Related: [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]].

%% ai-graph-start %%

**Related notes:**
- [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]]
- [[electron-builder asar requires asarUnpack for native .node addons]]
- [[Next.js production server never loads the native SWC binary at runtime]]
- [[Next.js .nextcache is a build-time-only cache, never bundle it]]
- [[electron-builder files node_modules glob disables devDependency pruning]]

%% ai-graph-end %%