---
title: "JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode"
created: 2026-06-07
type: observation
status: seedling
source: "LEO CDP memory A/B, 2026-06-07"
tags: [leo-cdp, jdk25, memory, footprint, docker]
---

# JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode

Measured on LEO CDP admin worker (same app, same Java-11 bytecode, identical 1200-request load, docker stats RSS): **JDK 25 runtime used ~30% less memory than JDK 11 - 289.2 vs 412.7 MiB**. No code or flag changes needed; this is pure runtime evolution (G1/metaspace/footprint ergonomics across 11->25). Adding -XX:+UseCompactObjectHeaders was neutral here (297.3 MiB) because the live-object population of an idle-ish admin worker is tiny - COH savings scale with object count, so judge it on data-pipeline/segmentation workloads with production-sized heaps. Practical implication: JDK 25 migration can shrink container memory requests/limits by ~25-30% for JVM services even before any code modernization.

## Related

- [[JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 AB]]
