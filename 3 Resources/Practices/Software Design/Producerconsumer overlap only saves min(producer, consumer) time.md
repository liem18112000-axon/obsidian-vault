---
title: "Producer/consumer overlap only saves min(producer, consumer) time"
created: 2026-06-14
type: lesson
status: seedling
source: "fb-info-project pipeline design 2026-06-14"
tags: [concurrency, pipeline, performance, design]
---

# Producer/consumer overlap only saves min(producer, consumer) time

Splitting a sequential two-phase job into a parallel **producer→consumer** pipeline can only hide `min(Σ producer_time, Σ consumer_time)` of wall-clock — the two phases now run concurrently, so the slower side still gates total runtime.

**Consequence:** before building the pipeline, *measure which phase dominates*. If one side is far cheaper than the other (e.g. collection takes 70 min, profile-visiting takes 5 min), overlapping them saves only ~the cheap side (~5 min) — a small gain for real threading/async complexity. The pipeline is only a big win when the two phases are *comparable* in cost.

**Where the real wins hide when phases are lopsided:**
- **Dedup/caching** in the expensive-output path (avoid repeat work), not overlap.
- **Parallelizing the dominant phase itself** (e.g. collect N items concurrently), not pipelining it against a cheap phase.

Concrete case: fb-info-project's scrape (comment-collection ≈70 min vs profile-visiting ≈short) — see [[Large FB group posts expand huge but article-scan yields almost no profiles]] and `docs/parallel-producer-consumer-plan.md`. The honest conclusion was: profile cache + fixing the slow collection beat the producer/consumer overlap.

**Rule of thumb:** profile the phases *first*; reach for a pipeline only when neither phase dominates. Otherwise attack the dominant phase directly.

## Related

- [[Large FB group posts expand huge but article-scan yields almost no profiles]]
