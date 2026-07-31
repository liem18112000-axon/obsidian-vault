---
ai_hash: bf03caa86b4f557e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities:
- Bytecode Target 25
- JVM
- k6 rounds
- Java-11-era source
- javac --release 25
- Class-file major 69
- Corretto 25 JVM
- Major-55 build
- Identical source
- RPS
- Median Latency
- P95 Latency
- Memory
- LEO CDP admin worker
- Modern JVM runtime
- Old class files
- Compatibility
- JDK 25 runtime
- LEO CDP container RSS
- JDK 11
source: LEO CDP Wave 0 batch 3, 2026-06-07
status: seedling
tags:
- leo-cdp
- jdk25
- bytecode
- javac
- k6
- performance
title: Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive
type: observation
---

# Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive

Recompiling the SAME Java-11-era source at javac --release 25 (class-file major 69) and running on the same Corretto 25 JVM swept all 3 interleaved k6 rounds vs the major-55 build of identical source: medians +8.3% rps (493.5 vs 455.5), -23% median latency (68.5 vs 88.6 ms), -7% p95; memory parity (287 MiB both, as expected - heap behavior is runtime-, not classfile-, dominated). LEO CDP admin worker, the only clean 3/3 sweep in the whole study. Magnitude is near the quiet-host noise band (+/-5-8%) so treat ~8% cautiously, but direction never reversed. Takeaway: once you commit to a modern JVM runtime, also raising the bytecode target is free-to-positive - there is no perf reason to keep emitting old class files for compatibility you no longer need.

## Related

- [[JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode]]

%% ai-graph-start %%

**Related notes:**
- [[JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode]]
- [[JDK 25 with CompactObjectHeaders beat JDK 11 by 7pct rps in LEO CDP k6 AB]]
- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]

**Relations:**
- Bytecode Target 25 — *applied to* — Java-11-era source
- javac --release 25 — *generates* — Class-file major 69
- Class-file major 69 — *runs on* — Corretto 25 JVM
- Corretto 25 JVM — *is a type of* — JVM
- Major-55 build — *is based on* — Identical source
- Bytecode Target 25 — *improved performance in* — k6 rounds
- Bytecode Target 25 — *increased* — RPS
- Bytecode Target 25 — *reduced* — Median Latency
- Bytecode Target 25 — *reduced* — P95 Latency
- Bytecode Target 25 — *maintained* — Memory
- LEO CDP admin worker — *demonstrated benefits of* — Bytecode Target 25
- Bytecode Target 25 — *is recommended for* — Modern JVM runtime
- Old class files — *offer* — Compatibility
- JDK 25 runtime — *reduced* — LEO CDP container RSS
- JDK 25 runtime — *compared to* — JDK 11
- JDK 25 runtime — *used same bytecode as* — JDK 11

%% ai-graph-end %%