---
title: "JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression"
created: 2026-06-07
type: lesson
status: seedling
source: "LEO CDP G2-local k6 A/B, 2026-06-07"
tags: [leo-cdp, jdk25, netty, k6, performance]
---

# JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression

Local k6 A/B (LEO CDP admin worker, identical warmup, 50VU/80s, zero errors both sides): Corretto 25 vs Corretto 11 showed **-15% throughput (325 vs 384 req/s) and +75% p95 (504 vs 288ms)** serving HTML + static JS through Vert.x 3.8.5/Netty 4.1.44 - even though Netty's PlatformDependent reports hasUnsafe=true and directBufferPreferred=true on JDK 25 (probed via jshell --class-path 'deps/*' inside the container). Lesson 1: 'boots clean with flags' does NOT mean 'performs at parity' - old-Netty-on-new-JDK regressions show up only under load. Lesson 2: jshell with the app's deps on the classpath is a cheap way to probe a library's runtime capability detection inside a container. Next diagnostic rungs: repeated longer runs (env noise), async-profiler flame diff, then Vert.x 3.9.16 (newer Netty) re-measure.

## Related

- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]

**CORRECTION (same day, after rigorous protocol):** the 15%/75% regression did NOT reproduce. An interleaved 3-round x 2-side protocol (150s steady state, fresh container + identical warmup each run, sides alternated) showed same-side round-to-round swings of +/-40% (jdk11: 279->503 rps) and JDK 25 outright WON round 1 (423 vs 279 rps). The single-run delta was host noise (Windows + Rancher Desktop/WSL). Revised lessons: (1) a dev laptop cannot resolve a +/-10% perf gate - run the precise gate on dedicated hardware; (2) never conclude from single runs - interleave repeated A/B rounds so host drift hits both sides; (3) the part that DID hold: zero errors across ~150k requests on JDK 25 - functional soundness was never in question.
