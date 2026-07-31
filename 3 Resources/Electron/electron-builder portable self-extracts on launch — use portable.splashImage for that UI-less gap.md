---
title: "electron-builder portable self-extracts on launch — use portable.splashImage for that UI-less gap"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15 vinnstack startup UX"
tags: [electron, electron-builder, portable, startup, splash, windows]
---

# electron-builder portable self-extracts on launch — use portable.splashImage for that UI-less gap

A packaged Vinnstack launch has TWO sequential wait phases, and each needs its own loader:

1. **Portable self-extraction (pre-code, UI-less).** electron-builder’s `portable` Windows target is a self-extracting archive: on first launch of a given version it unpacks the whole app to `%TEMP%` before any Electron/app code runs. With `asar:false` that is thousands of small files, each scanned by AV — several seconds to tens of seconds. No JS is running yet, so an in-app splash window CANNOT cover this phase. The only feedback available is `build.portable.splashImage` — a **.bmp** the extraction stub renders while unpacking. Subsequent launches of the same version reuse the extracted copy and skip this.

2. **In-process Next.js server (app code running).** After extraction, `electron/main.js` starts the Next production server in-process (`next({dev:false}).prepare()` + `listen`). Measured ~1.6s. This IS covered by the in-app Electron splash BrowserWindow (data: URL spinner) already in main.js.

**Lesson:** if users report "I click the exe and nothing shows up for a long time," the culprit is phase 1 (extraction), not the app — and the in-app splash is powerless there. Add `portable.splashImage`. Longer-term, re-enabling asar (with asarUnpack for the pieces Next needs) would collapse phase 1 from thousands of files to one, but that was backed out because Next couldn’t run from a read-only asar.

Config (path is relative to project root; put the bmp somewhere NOT gitignored — `/build` is ignored in this repo, so it lives at `public/brand/splash.bmp`):
```
"portable": { "splashImage": "public/brand/splash.bmp" }
```

## Related

- [[Vinnstack EXE release]]
- [[Windows holds file handles briefly after taskkill — rmSync EPERMs]]
- [[so retry]]
