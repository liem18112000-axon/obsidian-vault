---
ai_hash: ab6d0f61467fc24e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- JDK 25
- Netty
- k6
- LEO CDP admin worker
- Corretto 25
- Corretto 11
- Vert.x 3.8.5
- Netty 4.1.44
- PlatformDependent
- jshell
- async-profiler
- Vert.x 3.9.16
- Windows
- Rancher Desktop
- WSL
- Throughput regression (15%)
- p95 latency increase (75%)
- Functional soundness
- Host noise
- Clean boot
- Load testing
- Library runtime capability detection
- Flame diff
- A/B testing
- Single-run delta
- Round 1
- LEO CDP empirical result
source: LEO CDP G2-local k6 A/B, 2026-06-07
status: seedling
tags:
- leo-cdp
- jdk25
- netty
- k6
- performance
title: JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty
  regression
type: lesson
---

# JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression

Local k6 A/B (LEO CDP admin worker, identical warmup, 50VU/80s, zero errors both sides): Corretto 25 vs Corretto 11 showed **-15% throughput (325 vs 384 req/s) and +75% p95 (504 vs 288ms)** serving HTML + static JS through Vert.x 3.8.5/Netty 4.1.44 - even though Netty's PlatformDependent reports hasUnsafe=true and directBufferPreferred=true on JDK 25 (probed via jshell --class-path 'deps/*' inside the container). Lesson 1: 'boots clean with flags' does NOT mean 'performs at parity' - old-Netty-on-new-JDK regressions show up only under load. Lesson 2: jshell with the app's deps on the classpath is a cheap way to probe a library's runtime capability detection inside a container. Next diagnostic rungs: repeated longer runs (env noise), async-profiler flame diff, then Vert.x 3.9.16 (newer Netty) re-measure.

## Related

- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]

**CORRECTION (same day, after rigorous protocol):** the 15%/75% regression did NOT reproduce. An interleaved 3-round x 2-side protocol (150s steady state, fresh container + identical warmup each run, sides alternated) showed same-side round-to-round swings of +/-40% (jdk11: 279->503 rps) and JDK 25 outright WON round 1 (423 vs 279 rps). The single-run delta was host noise (Windows + Rancher Desktop/WSL). Revised lessons: (1) a dev laptop cannot resolve a +/-10% perf gate - run the precise gate on dedicated hardware; (2) never conclude from single runs - interleave repeated A/B rounds so host drift hits both sides; (3) the part that DID hold: zero errors across ~150k requests on JDK 25 - functional soundness was never in question.

%% ai-graph-start %%

**Related notes:**
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]
- [[Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive]]
- [[JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 AB]]
- [[JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode]]
- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]

**Relations:**
- JDK 25 — *requires* — Load testing
- Clean boot — *hid* — Throughput regression (15%)
- Clean boot — *hid* — p95 latency increase (75%)
- Throughput regression (15%) — *attributed to* — Netty
- k6 — *used for* — A/B testing
- A/B testing — *performed on* — LEO CDP admin worker
- Corretto 25 — *compared to* — Corretto 11
- Corretto 25 — *initially showed* — Throughput regression (15%)
- Corretto 25 — *initially showed* — p95 latency increase (75%)
- Vert.x 3.8.5 — *uses* — Netty 4.1.44
- Vert.x 3.8.5 — *serves through* — Netty 4.1.44
- PlatformDependent — *is part of* — Netty
- PlatformDependent — *reports* — hasUnsafe=true
- PlatformDependent — *reports* — directBufferPreferred=true
- PlatformDependent — *reports on* — JDK 25
- jshell — *probes* — Library runtime capability detection
- Netty 4.1.44 — *caused regressions on* — JDK 25
- async-profiler — *used for* — Flame diff
- Vert.x 3.9.16 — *uses* — Netty
- Vert.x 3.9.16 — *for* — re-measure
- Throughput regression (15%) — *did not reproduce* — CORRECTION
- p95 latency increase (75%) — *did not reproduce* — CORRECTION
- Single-run delta — *caused by* — Host noise
- Host noise — *includes* — Windows
- Host noise — *includes* — Rancher Desktop
- Host noise — *includes* — WSL
- JDK 25 — *won* — Round 1
- Functional soundness — *was* — not in question
- Vert.x 3.8.5 — *runs on* — JDK 25
- Netty 4.1.44 — *runs on* — JDK 25
- Vert.x 3.8.5 — *resulted in* — LEO CDP empirical result
- Netty 4.1.44 — *resulted in* — LEO CDP empirical result
- JDK 25 — *resulted in* — LEO CDP empirical result

%% ai-graph-end %%