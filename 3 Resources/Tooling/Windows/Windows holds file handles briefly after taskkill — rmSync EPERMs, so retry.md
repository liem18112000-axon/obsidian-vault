---
ai_hash: c475e8bd2eff887d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-15 vinnstack exe smoke harness
status: seedling
tags:
- windows
- nodejs
- electron
- taskkill
- eperm
- gotcha
title: Windows holds file handles briefly after taskkill — rmSync EPERMs, so retry
type: gotcha
---

# Windows holds file handles briefly after taskkill — rmSync EPERMs, so retry

On Windows, `taskkill /F /T` returns before the OS actually releases the killed process's open file handles (antivirus scanning and lazy handle release both add latency). A `fs.rmSync(dir, {recursive:true, force:true})` fired immediately afterward can throw `EPERM: operation not permitted, unlink ...` on a still-locked file — for Electron the usual culprit is `d3dcompiler_47.dll` — even though `force:true` is set (force ignores ENOENT, not EPERM).

**Fix:** sleep ~1s after taskkill, then wrap the delete in a small retry loop (5× with 500ms backoff) and swallow the final failure. A leftover temp dir must never fail an otherwise-green run; the OS temp cleaner reclaims it later.

```js
async function rmResilient(dir) {
  for (let i = 0; i < 5; i++) {
    try { fs.rmSync(dir, { recursive: true, force: true }); return; } catch { await sleep(500); }
  }
}
```

Surfaced writing `scripts/exe-release-smoke.mjs` (the Vinnstack EXE-release test harness): the harness extracts the packaged exe to a temp dir, launches the inner Electron binary, then taskkills it — and the cleanup EPERMd on the locked DLL, exiting 2 and masking 23/23 passing functional assertions.

## Related

- [[Vinnstack EXE release]]

%% ai-graph-start %%

**Related notes:**
- [[Windows child processes survive when only the parent is killed]]
- [[electron-builder portable self-extracts on launch — use portable.splashImage for that UI-less gap]]
- [[Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]
- [[An Electron GUI app can't be smoke-tested from a non-interactive automation session]]
- [[Electron GPU process launch failure is fatal; disable hardware acceleration to avoid it]]

%% ai-graph-end %%