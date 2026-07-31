---
title: "stopChunk() without a path silently discards that chunk's trace data"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23, luz-docs earchive-perf-trace tool"
tags: [playwright, tracing, gotcha, testing]
aliases: ["Page navigation timeout can corrupt Playwright context tracing state", "Manually stopChunk()-ing the last Playwright trace chunk breaks the final tracing.stop({path})"]
---

# stopChunk() without a path silently discards that chunk's trace data

**This note went through two wrong theories before landing on the actual mechanism — kept below as the history, since both intermediate "fixes" are natural things to try and both are traps.**

1. First guess: a `page.goto()` timeout during a trial corrupts the shared context's tracing state, later making `context.tracing.stop({path})` throw `Must start tracing before stopping`. Wrong — reproduced with zero navigation errors.
2. Second guess (closer, but still wrong in effect): the real trigger for that exact error is calling `context.tracing.stopChunk()` (no path) after the trial loop's LAST iteration, then calling `tracing.stop({path})` — `stop({path})` internally does `_doStopChunk(path)` to finalize+save whichever chunk is *currently open*; if you already manually stopped the last one, there's nothing open, so it throws. The "fix" tried was: don't stop the last chunk, leave it open for `stop({path})` to close. This does stop the crash — **but produces a trace.zip containing only that ONE trial**, silently. No error, just wrong/incomplete data.

**The actual mechanism**: `stopChunk()` called **without** a `path` option takes Playwright's discard branch (`if (!filePath) { tracingStopChunk({mode:'discard'}); return }`) — it throws away that chunk's recorded trace data outright. It does NOT buffer the chunk for a later combined save; each `startChunk()` call points at a fresh underlying trace file (`_allocateNewTraceFile`), so a discarded chunk's data is just gone. There is no "accumulate silently, save all at the end" mode — see [[Playwright has no way to merge N trials into one browsable trace.zip]] for why that whole idea doesn't work and what the actual correct pattern is (save every chunk with its own path, immediately, uniformly — no last-iteration special case).

**Takeaway beyond this specific case**: when an internal API state check fails only in a *specific sequence* of calls (crashes only at the end, or only sometimes), don't assume the trigger is whatever unrelated flaky thing happened to be running at the same moment (here: a real env timeout) — trace the actual call sequence first. The correlation looked causal for two separate multi-run test sessions before being ruled out by reproducing the exact same failure with a clean run.

## Related

- [[Playwright has no way to merge N trials into one browsable trace.zip]]
