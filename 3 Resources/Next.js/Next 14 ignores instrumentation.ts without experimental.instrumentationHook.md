---
title: "Next 14 ignores instrumentation.ts without experimental.instrumentationHook"
created: 2026-07-24
type: gotcha
status: seedling
source: "session 2026-07-24, Claude Routine build"
tags: [nextjs, instrumentation, startup, gotcha]
---

# Next 14 ignores instrumentation.ts without experimental.instrumentationHook

In **Next.js 14**, a root `instrumentation.ts` (exporting `register()`) is **NOT** picked up at server startup unless you set `experimental.instrumentationHook: true` in `next.config.mjs`. The hook became default-on only in **Next 15**.

Symptom if you forget: no error, no log — `register()` simply never runs, so whatever you started there (a scheduler, a warm-up, telemetry) silently does nothing. Easy to misdiagnose as a bug in the started code.

Pattern for a server-only startup side-effect:
- guard on runtime: `if (process.env.NEXT_RUNTIME !== 'nodejs') return;` (skip the edge runtime, which can't touch DB/fs);
- `await import()` the heavy module INSIDE register() so it isn't pulled into the edge bundle;
- make the started work idempotent (a module-level `started` singleton) because hot-reload can re-invoke register().

`register()` runs once per server process — identically under `next dev`, `next start`, and an Electron in-process Next server — which makes it the right single start point for a background loop across all three.

## Related

- [[Vinnstack Claude Routine architecture (night-shift scheduler)]]
