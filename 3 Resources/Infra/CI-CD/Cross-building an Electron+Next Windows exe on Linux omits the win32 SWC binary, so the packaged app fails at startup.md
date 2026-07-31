---
title: "Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [electron, nextjs, swc, cross-build, cloud-build, native-deps, gotcha]
---

# Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup

Root cause (Vinnstack release exe didn't work, 2026-07-14): the Windows portable exe is CROSS-BUILT on Linux (Cloud Build node:20 + wine for electron-builder --win). `npm ci` on Linux installs only the Linux platform-specific @next/swc-* native binary (swc-linux-x64-gnu / -musl). electron-builder then packaged THOSE into the Windows app (visible under resources/app.asar.unpacked/node_modules/@next/). There was NO @next/swc-win32-x64-msvc in the package. At runtime on Windows, next({dev:false}).prepare() can't load a usable SWC binary → throws → electron/main.js startNextServer() fails → app shows its error dialog and quits. Symptom seen: splash opens + cloud-sql-proxy sidecar starts (both before startNextServer), then nothing ever listens on the pinned port.

Why it hid: `next start` and programmatic next({dev:false}).prepare() BOTH work on the Windows dev machine (it has the win32 swc). A green Cloud Build (tests + next build + package + upload) does NOT catch it either — build succeeds on Linux with the linux swc; the mismatch only bites at runtime on the target OS.

Fix: after `npm ci` in CI, force-install the matching win32 swc so it's bundled — 
`NEXT_VER=$(node -p "require('next/package.json').version"); npm install --no-save --force "@next/swc-win32-x64-msvc@${NEXT_VER}"`. 
--force bypasses the EBADPLATFORM check on Linux; --no-save keeps the lockfile valid for `npm ci`; version pinned to next's own version so it can't drift. The build's asarUnpack `node_modules/@next/swc-*/**/*` glob then unpacks it (native .node can't live inside asar).

General lesson: any platform-specific native optional dependency (@next/swc, esbuild, sharp, @swc/core, better-sqlite3, etc.) is resolved for the BUILD host's OS/arch. Cross-building a desktop app for another OS must explicitly add the target-OS native package, or build on the target OS. Verify by listing the packaged artifact for the expected platform binary (e.g. grep the app for swc-win32) — don't trust a green build.

How it was found: extracted the release exe with 7za and listed resources/app.asar.unpacked → saw only linux swc; grep for "swc-win32" returned 0.
