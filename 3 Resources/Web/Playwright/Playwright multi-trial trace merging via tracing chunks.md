---
title: "Playwright has no way to merge N trials into one browsable trace.zip"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23, luz-docs earchive-perf-trace tool"
tags: [playwright, tracing, testing, gotcha]
aliases:
  - "Playwright multi-trial trace merging via tracing chunks"
  - "stopChunk() without a path silently discards that chunk's trace data"
  - "Manually stopChunk()-ing the last Playwright trace chunk breaks the final tracing.stop({path})"
  - "Page navigation timeout can corrupt Playwright context tracing state"
---

# Playwright has no way to merge N trials into one browsable trace.zip

`context.tracing.startChunk()` / `stopChunk()` (no path) + a final `context.tracing.stop({path})` does **not** bundle every trial into one multi-tab trace.zip. Verified against Playwright source (`node_modules/playwright-core/lib/coreBundle.js`):

- **`stopChunk()` without a `path` discards that chunk outright** — `if (!filePath) { tracingStopChunk({mode:'discard'}); return }`. It does not buffer for a later combined save, and each `startChunk()` allocates a fresh trace file (`_allocateNewTraceFile`), so the data is simply gone. Only the chunk still open when `stop({path})` runs is saved (empirically: the "combined" zip held only the LAST trial's `context-options` marker).
- `show-trace`'s positional arg is `[trace]` — commander.js parses one optional value, not variadic. You cannot open several traces as tabs.
- Pointing `show-trace` at a directory works only for ONE **unzipped** trace's loose `.trace`/`.network`/`resources/` files (`traceDescriptor()` lists that one directory) — not a folder of `trace.zip` archives.

**Related failure mode:** `stop({path})` internally does `_doStopChunk(path)` on whichever chunk is *currently open*; if you already manually stopped the last chunk, nothing is open and it throws `Must start tracing before stopping`. The tempting "fix" — leave the last chunk open for `stop({path})` — stops the crash but silently yields a trace.zip containing only that one trial. (A `page.goto()` timeout in the loop is a red herring; reproduced with zero navigation errors.)

**What chunks are actually for:** several recordings on ONE shared `BrowserContext` (cheaper than relaunching per trial) with a readable per-chunk `title` — either discard a chunk or save it as its own zip. Not a merge unit.

**Correct pattern — N trials, keep every trace:** save every chunk with its own path, uniformly, no last-iteration special case:
```js
for (let i = 1; i <= N; i++) {
  await context.tracing.startChunk({ title: `run${i}` })
  try { /* run trial i on a fresh context.newPage() */ }
  finally { await context.tracing.stopChunk({ path: `run${i}/trace.zip` }) }
}
await context.tracing.stop()   // no path — every trial already saved; this just tears down
```
Open one at a time: `npx playwright show-trace run3/trace.zip`.

**Debugging takeaway:** when an internal API state check fails only in a *specific call sequence*, trace the call sequence — don't blame the unrelated flaky thing (a real env timeout) that happened to co-occur; the correlation looked causal across two sessions before a clean run ruled it out.

## Related

- [[Server-rendered JSF apps never expose their internal API calls to browser network capture]]
