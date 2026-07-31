---
ai_hash: 2177297f5bb552a7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- nextjs
- windows
- offline-build
- gotcha
- deployment
title: Next.js standalone bundle breaks when the dot-prefixed .next folder is dropped
  in transfer
type: gotcha
---

# Next.js standalone bundle breaks when the dot-prefixed .next folder is dropped in transfer

A Next.js `output: 'standalone'` bundle run on Windows fails at startup with `Error: Could not find a production build in the './.next' directory` (next.js docs: production-start-no-build-id) when the build's `.next` folder is missing from the deployed `app/` directory.

**Why it happens:** the standalone `server.js` does `process.chdir(__dirname)` and is configured with `distDir: "./.next"`, so at runtime it resolves the build from `app/.next/BUILD_ID`. The packaging/copy itself is usually correct — the folder gets dropped *in transfer*. `.next` is **dot-prefixed**, and Windows Explorer (with 'hidden items' off), some zip/copy tools, OneDrive sync and antivirus silently skip it when copying a loose folder. You end up with `app/` but no (or empty) `app/.next`.

**Prevention (three layers):**
1. Distribute the bundle as a single **`.zip`** — `Compress-Archive` preserves `.next` reliably. Don't loose-copy the folder via Explorer.
2. At package time, **assert `app/.next/BUILD_ID` exists** and fail the build if not, so a broken bundle never ships.
3. Guard the launcher `.bat`: `if not exist "%~dp0app\.next\BUILD_ID"` print a plain 'bundle incomplete — .next didn't transfer' message and exit, instead of the cryptic Next.js error.

The same dot-folder transfer trap applies to any tool that emits dot-prefixed output dirs on Windows.

Project: HoSoBaiGiang frontend-application, `scripts/offline/`.

%% ai-graph-start %%

**Related notes:**
- [[Next.js standalone Docker image must copy public and .next static next to server.js]]
- [[Two next dev instances sharing one .next corrupt the webpack PackFileCache]]
- [[Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]
- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]

%% ai-graph-end %%