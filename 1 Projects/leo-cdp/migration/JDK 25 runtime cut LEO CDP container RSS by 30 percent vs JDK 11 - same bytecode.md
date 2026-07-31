---
ai_hash: e4b92516a6f051dc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- JDK 25
- LEO CDP
- RSS
- JDK 11
- G1
- Metaspace
- Footprint ergonomics
- UseCompactObjectHeaders
- Container memory requests/limits
- JVM services
- k6 AB
- rps
source: LEO CDP memory A/B, 2026-06-07
status: seedling
tags:
- leo-cdp
- jdk25
- memory
- footprint
- docker
title: JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode
type: observation
---

# JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode

Measured on LEO CDP admin worker (same app, same Java-11 bytecode, identical 1200-request load, docker stats RSS): **JDK 25 runtime used ~30% less memory than JDK 11 - 289.2 vs 412.7 MiB**. No code or flag changes needed; this is pure runtime evolution (G1/metaspace/footprint ergonomics across 11->25). Adding -XX:+UseCompactObjectHeaders was neutral here (297.3 MiB) because the live-object population of an idle-ish admin worker is tiny - COH savings scale with object count, so judge it on data-pipeline/segmentation workloads with production-sized heaps. Practical implication: JDK 25 migration can shrink container memory requests/limits by ~25-30% for JVM services even before any code modernization.

## Related

- [[JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 AB]]

%% ai-graph-start %%

**Related notes:**
- [[JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 AB]]
- [[Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive]]
- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]
- [[Use JDK_JAVA_OPTIONS for JVM flags in container images]]

**Relations:**
- JDK 25 — *reduces* — LEO CDP container RSS
- JDK 25 — *reduces RSS by* — 30 percent
- JDK 25 — *compared to* — JDK 11
- JDK 25 — *used* — 289.2 MiB
- JDK 11 — *used* — 412.7 MiB
- Memory reduction — *attributed to* — G1
- Memory reduction — *attributed to* — Metaspace
- Memory reduction — *attributed to* — Footprint ergonomics
- UseCompactObjectHeaders — *resulted in* — 297.3 MiB
- UseCompactObjectHeaders — *impact was* — neutral
- UseCompactObjectHeaders — *savings scale with* — object count
- JDK 25 migration — *can shrink* — Container memory requests/limits
- Container memory requests/limits — *shrink by* — 25-30%
- JDK 25 — *beats* — JDK 11
- JDK 25 — *beats JDK 11 by* — 7pct rps
- JDK 25 — *in* — LEO CDP k6 AB
- JDK 25 — *with* — UseCompactObjectHeaders

%% ai-graph-end %%