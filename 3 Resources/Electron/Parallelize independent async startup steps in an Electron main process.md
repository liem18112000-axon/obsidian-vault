---
title: "Parallelize independent async startup steps in an Electron main process"
created: 2026-07-08
type: lesson
status: seedling
source: "vinnstack build optimization session"
tags: [electron, performance, async, startup]
---

# Parallelize independent async startup steps in an Electron main process

When an Electron app's startup sequence has multiple independent async steps before the window can open (e.g. starting a sidecar process, and separately preparing an embedded server), run them with `Promise.all` instead of `await`-ing one after another — the total wait becomes the max of the two instead of their sum.

Whether two steps are actually independent depends on what each one *touches*, not just what it *returns*: in a Vinnstack (Next.js + Electron) app, starting a Cloud SQL Auth Proxy sidecar and calling `nextApp.prepare()` looked sequential in the original code (proxy first, then the Next server as part of window creation), but `prepare()` only compiles/loads the app in-process — it doesn't touch the database. Only requests handled *after* the window opens hit the DB. So the two had no real dependency and could run concurrently, cutting real startup latency by roughly the smaller of the two wait times.

Gotcha when doing this refactor: if a "create window" function used to compute its own dependency (e.g. resolving a server URL) internally, moving that computation out to run in parallel means every OTHER caller of that function (e.g. an OS-level `app.on("activate")` handler that re-opens a window) needs to be updated to supply the same value explicitly — otherwise it silently gets `undefined` instead of erroring at compile time (plain JS, no type checker to catch it).
