---
title: "Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup"
created: 2026-07-15
updated: 2026-07-31
type: lesson
status: seedling
source: "session 2026-07-14 (Vinnstack release exe)"
tags: [electron, nextjs, swc, cross-build, cloud-build, native-deps, gotcha]
---

# Cross-building an Electron+Next Windows exe on Linux omits the win32 SWC binary, so the packaged app fails at startup

**Claim:** platform-specific native optional deps resolve for the BUILD host's OS/arch. `npm ci` on a Linux CI image installs only `@next/swc-linux-x64-gnu`/`-musl`; electron-builder then packages *those* into the Windows app, with no `@next/swc-win32-x64-msvc`. On Windows, `next({dev:false}).prepare()` cannot load a usable SWC binary → throws → `startNextServer()` in `electron/main.js` fails and the app quits. Symptom: splash + cloud-sql-proxy sidecar start (both run before `startNextServer`), then nothing ever listens on the pinned port.

**Why it hides:** the Windows dev machine has the win32 swc, so `next start` and the programmatic path both work locally; and Cloud Build stays green because the Linux build genuinely succeeds. The mismatch only bites at runtime on the target OS.

**Fix — force-install the target-OS binary after `npm ci` in CI:**

```bash
NEXT_VER=$(node -p "require('next/package.json').version")
npm install --no-save --force "@next/swc-win32-x64-msvc@${NEXT_VER}"
```

`--force` bypasses the `EBADPLATFORM` check on Linux; `--no-save` keeps the lockfile valid for `npm ci`; pinning to next's own version prevents drift. The `asarUnpack` glob `node_modules/@next/swc-*/**/*` then unpacks it (a native `.node` cannot live inside asar).

**Generalise:** same for `esbuild`, `sharp`, `@swc/core`, `better-sqlite3`. Either add the target-OS native package explicitly or build on the target OS — and **verify the artifact, not the build**: extract the exe (`7za`) and grep `resources/app.asar.unpacked` for the expected platform binary (e.g. `swc-win32`).

## Related

- [[Cross-building Electron Windows exe on Linux needs wine]]
