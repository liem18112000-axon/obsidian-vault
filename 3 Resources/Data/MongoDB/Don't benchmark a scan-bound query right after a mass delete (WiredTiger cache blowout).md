---
title: "Don't benchmark a scan-bound query right after a mass delete (WiredTiger cache blowout)"
created: 2026-06-19
type: lesson
status: seedling
source: "session 2026-06-19 LUZ-154613"
tags: [mongodb, performance, benchmarking, gotcha, wiredtiger]
---

# Don't benchmark a scan-bound query right after a mass delete (WiredTiger cache blowout)

After a large `deleteMany` (here: trimming a Mongo collection from 960k down to 480k via repeated $sample deletes), a scan-bound query that previously ran in ~94 s timed out at >300 s for **every** fan-out width K=1..12.

**Why:** mass deletes evict the working set from the WiredTiger cache and churn the oplog, so the next scan-bound count goes disk-bound — 3x+ slower — until the cache re-warms. The earlier fast figure was measured on a freshly, sequentially-seeded collection (warm cache).

**How to apply:** never benchmark a scan-bound query immediately after a big delete. Either (a) re-warm first — run the query until one returns near the expected time, then start timing, (b) re-seed sequentially instead of deleting down to the target size, or (c) measure against a cache/index path that skips the scan. Also: CPU bursts in `kubectl top` (e.g. 3 cores) prove the scan IS running even when the request times out — distinguish 'genuinely scanning but slow' from 'cold-pod fast-500'.

Related: [[earchive cascade mode]].
