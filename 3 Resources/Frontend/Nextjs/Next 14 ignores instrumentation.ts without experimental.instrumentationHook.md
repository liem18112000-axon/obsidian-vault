---
ai_hash: 1d03ffd0d6383f88
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities: []
source: session 2026-07-24, Claude Routine build
status: seedling
tags:
- nextjs
- instrumentation
- startup
- gotcha
title: Next 14 ignores instrumentation.ts without experimental.instrumentationHook
type: gotcha
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

%% ai-graph-start %%

**Related notes:**
- [[instrumentation.ts with a Node-only dep breaks the edge build - externalize it]]
- [[Vinnstack Claude Routine architecture (night-shift scheduler)]]
- [[Config read into a module-level const applies only on next process launch]]
- [[Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]
- [[Next.js dev server webpack chunk cache corrupts after many route addsdeletes]]

%% ai-graph-end %%