---
ai_hash: 2a0b0c85c51f7618
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-16
entities: []
source: Vinnstack session 2026-07-15
status: seedling
tags:
- electron
- nextjs
- ipc
- architecture
- vinnstack
title: Surface Electron app version to an in-process Next server via process.env before
  prepare()
type: howto
---

# Surface Electron app version to an in-process Next server via process.env before prepare()

When an Electron app runs its Next.js UI as an **in-process** server (electron/main.js does `require('next')`, `next({dev:false}).prepare()`, then serves via http in the SAME process as the Electron main), you can hand runtime values from Electron to Next server code (API routes, server components) simply by setting `process.env.*` in main.js **before** calling `prepare()`. Route handlers read them at request time from the shared process env — no preload, no contextBridge, no IPC needed (which matters when `contextIsolation:true, nodeIntegration:false` and there is no preload script).

**Concrete use:** expose the real installed version. main.js: `process.env.VINNSTACK_APP_VERSION = app.getVersion()` before prepare; route `/api/version` returns `process.env.VINNSTACK_APP_VERSION || pkgVersion`. The client fetches `/api/version`.

**Why not NEXT_PUBLIC_*:** those are inlined at BUILD time, so setting them at runtime in main.js would NOT reach the client bundle — a server-read (API route / server component) of a plain (non-public) env var is required for runtime values.

**Dev fallback:** in `next dev` (ELECTRON_DEV=1) Next runs as a SEPARATE process, so the env var isn't set — have the route fall back to importing `package.json` version (`resolveJsonModule` + `@/*` path alias).

Context: Vinnstack, Electron 32 + Next 14, no preload.

## Related

- [[Electron]]

%% ai-graph-start %%

**Related notes:**
- [[Packaged Electron+Next.js API routes must not use process.cwd() for bundled files]]
- [[Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]]
- [[Parallelize independent async startup steps in an Electron main process]]
- [[Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]

%% ai-graph-end %%