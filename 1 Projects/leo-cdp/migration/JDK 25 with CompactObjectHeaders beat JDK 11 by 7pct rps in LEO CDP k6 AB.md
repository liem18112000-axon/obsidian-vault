---
title: "JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 A/B"
created: 2026-06-07
type: observation
status: seedling
source: "LEO CDP COH A/B, 2026-06-07"
tags: [leo-cdp, jdk25, compact-object-headers, jep519, k6, performance]
---

# JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 A/B

Second interleaved k6 batch (LEO CDP admin, 3 rounds x 150s steady, quieter host, same-side spread only +/-5-8%): **JDK 25 with -XX:+UseCompactObjectHeaders (JEP 519) beat JDK 11 on medians: 504.5 vs 470.2 rps (+7%) and p95 144 vs 184 ms (-22%)**, 0 functional errors. Two independent batches now lean JDK-25-faster in the same direction (plain 25 was +10% median rps). COH is a 25-only structural advantage - the flag does not exist on JDK 11 (it would crash the JVM, so A/B harnesses must inject it per-side, e.g. via a docker-compose.override.yml that is written/removed when swapping sides). Verify activation with: docker exec <c> jcmd 1 VM.flags | grep CompactObjectHeaders. Caveat: this surface is I/O-bound; COH's main payoff (smaller object headers -> less heap/GC on object-churn paths like profile merge/segmentation) still needs a data-pipeline benchmark.

## Related

- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]
