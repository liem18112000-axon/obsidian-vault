---
title: "Unsigned asar:false Electron app: ~30s first-launch delay is Defender scanning loose files"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15 vinnstack startup profiling"
tags: [electron, electron-builder, asar, windows-defender, startup, performance]
---

# Unsigned asar:false Electron app: ~30s first-launch delay is Defender scanning loose files

A packaged, UNSIGNED Electron app built with `asar:false` can take tens of seconds to show its window on FIRST launch — even though the app code itself is fast — because Windows Defender real-time protection scans every one of the thousands of freshly-written app files the first time they are executed/opened.

**Measured (Vinnstack, ~38,000 files in node_modules):** from spawning the extracted inner binary to `main.js` running was **~34.6s on first launch** but **~2.8s on the second launch of the same extracted copy** (Defender scan cached). The Next server then listened <1s after main.js. So the whole ~32s was first-touch AV, not our code and not extraction (files were already on disk before spawn).

**Symptom shape:** user double-clicks, sees the portable extraction splash, splash closes when extraction finishes, then a long dead gap with NO window (Electron is initialising while Defender scans) before the app finally appears. Feels like "it never starts." No in-app splash can cover this gap because none of the app JS is running yet.

**Fixes, best first:**
- **Code-sign** the exe — Defender trusts a signed binary and scans far less aggressively (needs a cert). This is the only real cure.
- A Defender path exclusion helps but isn’t something you ship (useful for your own testing loop only).
- Set expectations: SECOND and later launches of the SAME version are fast regardless (~2.8s, scan cached; portable reuses its `%TEMP%` extraction), so this only bites the first run after each new build. Tell users "first launch takes ~40s."

**⚠️ `asar:true` does NOT fix this — TESTED and disproven.** The intuition ("one file → Defender scans one thing instead of 38k") is wrong here: the scan is **byte-bound, not file-count-bound**. Measured on the same app, fresh-extract cold scan: **~34.6s with `asar:false` (~38k loose files) vs ~47.2s with `asar:true` (401 loose files, node_modules packed into a ~100MB `app.asar`)**. asar was if anything slightly SLOWER — Defender still scans the ~113MB of bytes, and Electron adds an integrity hash of the blob on load. So asar reduces file count / extraction churn but is neutral-to-negative for first-launch time. Don't reach for it to solve AV-scan startup. (asar can still be a reasonable default for other reasons; just not this one. Note for Next: `.next/**` and native `**/*.node` must be `asarUnpack`ed or the server won't run.)

## Related

- [[electron-builder portable self-extracts on launch — use portable.splashImage for that UI-less gap]]
