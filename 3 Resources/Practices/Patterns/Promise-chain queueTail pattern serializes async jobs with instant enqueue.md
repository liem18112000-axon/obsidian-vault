---
title: "Promise-chain queueTail pattern serializes async jobs with instant enqueue"
created: 2026-07-07
type: howto
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [nodejs, async, queue, concurrency, pattern]
---

# Promise-chain queueTail pattern serializes async jobs with instant enqueue

A `Promise` chain variable (e.g. `queueTail: Promise<void> = Promise.resolve()`) is a lightweight way to serialize async jobs — one at a time, in submission order — without a real job queue library or worker process.

The pattern: keep one module-level `queueTail` promise. To enqueue work, do `queueTail = queueTail.then(async () => { ...the real work... })` and return immediately (optionally after creating a small status object like `{ phase: "queued" }` that the async closure mutates as it progresses). Callers get an instant response with a ticket/handle; the actual work runs strictly sequentially because each `.then()` only starts once the prior one resolves. A separate in-memory `Map<key, JobStatus>` (or similar) lets callers poll `status.phase` for progress without awaiting completion.

This is exactly the shape needed whenever you want "let the user queue up N requests, but only run one at a time, in order" — e.g. Vinnstack's Graphify feature does this in `lib/graphifyRunner.ts` (`jobs` Map + `queueTail`, `refreshRepo()`) to serialize repository scans, and `lib/contentRepo.ts` uses a simpler version of the same idea to serialize git operations. Worth reaching for whenever an API route currently awaits a slow operation synchronously and you want to (a) return instantly and (b) guarantee no two runs interleave.

## Related

- [[Graphify acquires repo source gitless via Bitbucket tarball download]]
