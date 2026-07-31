---
title: "Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters"
created: 2026-07-21
type: lesson
status: seedling
source: "trace run 2026-07-21"
tags: [earchive, playwright, gotcha]
---

# Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters

In the eArchive folder-drill view there is no "Documents (N)" / "Custom (M)" counter, and clicking a folder is not a full navigation (no "Manage access rights" re-render). The trace tool's collectPageEnter completion check required both counters, so scenario 4 always burned its full 90 s poll timeout — plus another 90 s waiting for "Manage access rights" — making the folder scenario take ~3 minutes of pure timeout on top of real load time.

**Fixed (2026-07-21)** in `trace-earchive.js`: collectPageEnter gained a `requireCounts` param (passed `false` for folder drills, so the done-check drops the two counters), and the "Manage access rights" wait after the folder click was **removed outright** — the folder view header is back-arrow + folder name + "K Files" and never renders that landmark, so gating the wait on navigation detection (tried URL compare, then a JS-heap marker) was moot: the wait can never succeed there. collectPageEnter's readRec polling alone handles readiness, including a full nav re-injecting the harness. Saves ~3 min per run.

## Related

- [[1 Projects/luz-docs/earchive/performance/Dev eArchive baseline items in 6s but count badges take 22-41s]]
- [[Playwright full-nav detection needs a JS-heap marker not URL compare]]
