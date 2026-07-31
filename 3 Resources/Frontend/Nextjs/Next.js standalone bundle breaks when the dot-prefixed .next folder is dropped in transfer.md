---
title: "Next.js standalone bundle breaks when the dot-prefixed .next folder is dropped in transfer"
created: 2026-06-19
type: gotcha
status: seedling
source: "session 2026-06-19"
tags: [nextjs, windows, offline-build, gotcha, deployment]
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
