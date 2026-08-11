---
ai_hash: 97819f7adf37d8a8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- JDK 25
- CompactObjectHeaders
- JDK 11
- LEO CDP
- k6
- JEP 519
- JVM
- Netty
- docker-compose.override.yml
- jcmd 1 VM.flags
- object-churn paths
- profile merge
- segmentation
- data-pipeline benchmark
- rps
- p95 latency
- functional errors
- object headers
- heap/GC
- JDK 25 perf parity
- clean boot
- Netty regression
- JDK 25 with CompactObjectHeaders
source: LEO CDP COH A/B, 2026-06-07
status: seedling
tags:
- leo-cdp
- jdk25
- compact-object-headers
- jep519
- k6
- performance
title: JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 A/B
type: observation
---

# JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 A/B

Second interleaved k6 batch (LEO CDP admin, 3 rounds x 150s steady, quieter host, same-side spread only +/-5-8%): **JDK 25 with -XX:+UseCompactObjectHeaders (JEP 519) beat JDK 11 on medians: 504.5 vs 470.2 rps (+7%) and p95 144 vs 184 ms (-22%)**, 0 functional errors. Two independent batches now lean JDK-25-faster in the same direction (plain 25 was +10% median rps). COH is a 25-only structural advantage - the flag does not exist on JDK 11 (it would crash the JVM, so A/B harnesses must inject it per-side, e.g. via a docker-compose.override.yml that is written/removed when swapping sides). Verify activation with: docker exec <c> jcmd 1 VM.flags | grep CompactObjectHeaders. Caveat: this surface is I/O-bound; COH's main payoff (smaller object headers -> less heap/GC on object-churn paths like profile merge/segmentation) still needs a data-pipeline benchmark.

## Related

- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]

%% ai-graph-start %%

**Related notes:**
- [[JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode]]
- [[Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive]]
- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]
- [[Use JDK_JAVA_OPTIONS for JVM flags in container images]]

**Relations:**
- JDK 25 — *uses* — CompactObjectHeaders
- CompactObjectHeaders — *is* — JEP 519
- JDK 25 with CompactObjectHeaders — *achieved* — 7% rps advantage over JDK 11
- JDK 25 with CompactObjectHeaders — *achieved* — 22% p95 latency improvement over JDK 11
- LEO CDP — *tested with* — k6
- CompactObjectHeaders — *is a feature of* — JDK 25
- CompactObjectHeaders — *not supported by* — JDK 11
- CompactObjectHeaders — *crashes* — JVM
- docker-compose.override.yml — *injects* — CompactObjectHeaders
- jcmd 1 VM.flags — *verifies activation of* — CompactObjectHeaders
- LEO CDP — *is* — I/O-bound
- CompactObjectHeaders — *reduces size of* — object headers
- object headers reduction — *leads to* — less heap/GC
- less heap/GC — *benefits* — object-churn paths
- object-churn paths — *include* — profile merge
- object-churn paths — *include* — segmentation
- CompactObjectHeaders — *needs* — data-pipeline benchmark
- JDK 25 — *achieved* — 10% median rps advantage over JDK 11
- JDK 25 with CompactObjectHeaders — *had* — 0 functional errors
- JDK 25 perf parity — *related to* — Netty regression
- Netty regression — *was* — 15 percent
- clean boot — *hid* — Netty regression

%% ai-graph-end %%