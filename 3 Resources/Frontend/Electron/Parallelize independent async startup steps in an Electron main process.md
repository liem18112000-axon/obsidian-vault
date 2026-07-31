---
ai_hash: ffc1df78a4264251
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities: []
source: vinnstack build optimization session
status: seedling
tags:
- electron
- performance
- async
- startup
title: Parallelize independent async startup steps in an Electron main process
type: lesson
---

# Parallelize independent async startup steps in an Electron main process

When an Electron app's startup sequence has multiple independent async steps before the window can open (e.g. starting a sidecar process, and separately preparing an embedded server), run them with `Promise.all` instead of `await`-ing one after another — the total wait becomes the max of the two instead of their sum.

Whether two steps are actually independent depends on what each one *touches*, not just what it *returns*: in a Vinnstack (Next.js + Electron) app, starting a Cloud SQL Auth Proxy sidecar and calling `nextApp.prepare()` looked sequential in the original code (proxy first, then the Next server as part of window creation), but `prepare()` only compiles/loads the app in-process — it doesn't touch the database. Only requests handled *after* the window opens hit the DB. So the two had no real dependency and could run concurrently, cutting real startup latency by roughly the smaller of the two wait times.

Gotcha when doing this refactor: if a "create window" function used to compute its own dependency (e.g. resolving a server URL) internally, moving that computation out to run in parallel means every OTHER caller of that function (e.g. an OS-level `app.on("activate")` handler that re-opens a window) needs to be updated to supply the same value explicitly — otherwise it silently gets `undefined` instead of erroring at compile time (plain JS, no type checker to catch it).

%% ai-graph-start %%

**Related notes:**
- [[electron-builder portable self-extracts on launch — use portable.splashImage for that UI-less gap]]
- [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]
- [[Open-and-degrade beats hard-quit let a desktop app start without its optional DB (Vinnstack clean-Win fix)]]
- [[Surface Electron app version to an in-process Next server via process.env before prepare()]]
- [[Unsigned Electron app first-launch transient Cannot find module during Defender post-install scan]]

%% ai-graph-end %%