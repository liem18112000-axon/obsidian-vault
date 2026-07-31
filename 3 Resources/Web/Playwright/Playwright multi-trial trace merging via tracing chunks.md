---
title: "Playwright has no way to merge N trials into one browsable trace.zip"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23, luz-docs earchive-perf-trace tool"
tags: [playwright, tracing, testing]
aliases: ["Playwright multi-trial trace merging via tracing chunks"]
---

# Playwright has no way to merge N trials into one browsable trace.zip

**Corrects an earlier wrong assumption in this same note.** I originally believed `context.tracing.startChunk()`/`stopChunk()` (no path) + a final `context.tracing.stop({path})` would bundle every trial's chunk into one trace.zip that the viewer shows as multiple tabs. Verified false by reading Playwright's own source (`node_modules/playwright-core/lib/coreBundle.js`):

- `stopChunk()` called **without** a path discards that chunk's recorded trace data outright (`if (!filePath) { tracingStopChunk({mode:'discard'}); return }`) — it does not buffer it for a later combined save. Only the chunk that's still open when you finally call `context.tracing.stop({path})` gets saved (confirmed empirically: a "combined" trace.zip built this way only ever contained the LAST trial's `context-options` marker, not all of them).
- The `show-trace` CLI command's positional argument is `[trace]` — commander.js parses it as a single optional value, not variadic. There is no way to pass several trace files and have them open together as tabs/sources.
- Pointing `show-trace` at a directory only works for one **unzipped** trace's loose `.trace`/`.network`/`resources/` files (`traceDescriptor()` just lists that one directory's contents) — not a folder of several separate `trace.zip` archives.

**What tracing chunks are actually for**: multiple recordings on ONE shared `BrowserContext` (e.g. Playwright Test's own per-retry trace), where you explicitly want either (a) to discard a chunk (`stopChunk()`, no path) or (b) save that one chunk as its own trace.zip (`stopChunk({path})`) before starting the next. Chunks give you a shared context (cheaper than relaunching a browser per trial) and a readable per-chunk `title` — they don't give you a merged view.

**Correct pattern for "N trials, keep every trace"**: reuse one context, but call `stopChunk({path: 'runN/trace.zip'})` for every trial including the last (uniformly, no special-casing) — each is a fully valid, independently-openable trace:
```js
for (let i = 1; i <= N; i++) {
  await context.tracing.startChunk({ title: `run${i}` })   // just a readable title, not a merge unit
  try { /* run trial i on a fresh context.newPage() */ }
  finally { await context.tracing.stopChunk({ path: `run${i}/trace.zip` }) }
}
await context.tracing.stop()   // no path — every trial already saved its own; this just tears down cleanly
```
Open one at a time: `npx playwright show-trace run3/trace.zip`.

See [[stopChunk() without a path silently discards that chunk's trace data]] for the specific discard-mode fact this correction turned on.

## Related

- [[stopChunk() without a path silently discards that chunk's trace data]]
