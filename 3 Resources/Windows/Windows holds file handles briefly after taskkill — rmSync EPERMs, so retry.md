---
title: "Windows holds file handles briefly after taskkill — rmSync EPERMs, so retry"
created: 2026-07-15
type: gotcha
status: seedling
source: "session 2026-07-15 vinnstack exe smoke harness"
tags: [windows, nodejs, electron, taskkill, eperm, gotcha]
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
