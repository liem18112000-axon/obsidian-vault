---
ai_hash: 5b6c497446ea5e9d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14
status: seedling
tags:
- async
- non-blocking
- nodejs
- concurrency
- pattern
- vinnstack
title: 'Non-blocking usage capture: fire-and-forget async writes + a serialized promise
  queue for race-safe RMW'
type: lesson
---

# Non-blocking usage capture: fire-and-forget async writes + a serialized promise queue for race-safe RMW

Requirement (Vinnstack, 2026-07-14): usage capture/compute must NEVER block the main action (the chat stream / a generation). Applied across every capture site.

Two patterns:
1) Pure append (usageLog.recordUsage) — fire-and-forget async: `void (async () => { try { await mkdir(...); await appendFile(...) } catch {} })()`. Safe under concurrency without a lock because each appendFile is atomic (O_APPEND). Callers keep a `void` signature and never await.
2) Read-modify-write tallies (bgSpend.recordBgSpend, skillUsage.recordSkillRead) — fire-and-forget BUT serialized via a module-level promise chain: `let q = Promise.resolve(); export function record(){ q = q.then(async () => { const s = load(); s.x++; await writeFile(tmp); await rename(tmp,file) }) }`. The queue prevents concurrent RMW from losing updates (the old code relied on SYNC read-modify-write being "atomic per event loop" — going async needs the queue to keep that guarantee).

Biggest gotcha found: skillUsage.recordSkillRead ran on the STREAMING hot path (once per Read tool_use) and did a synchronous readdirSync+statSync scan of 3 dirs + read + write — blocking the event loop mid-stream. Keep the cheap sync early-out (regex on the filename) BEFORE the queue, then do all fs work async inside the queued step.

Also: place the capture call AFTER the response is finalized (e.g. after safeClose() in the stream) so even the sync prep isn't in the critical path. Read/aggregate paths (readUsage, summarizeUsage, getUsage) can stay sync — they run in their OWN GET request, not the main action.

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]
- [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Client-side generation queue lets independent items run without blocking each other]]
- [[Vinnstack skill-usage counter missed reads past a 4MB stdout cap]]

%% ai-graph-end %%