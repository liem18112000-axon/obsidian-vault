---
title: "Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive"
created: 2026-06-07
type: observation
status: seedling
source: "LEO CDP Wave 0 batch 3, 2026-06-07"
tags: [leo-cdp, jdk25, bytecode, javac, k6, performance]
---

# Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive

Recompiling the SAME Java-11-era source at javac --release 25 (class-file major 69) and running on the same Corretto 25 JVM swept all 3 interleaved k6 rounds vs the major-55 build of identical source: medians +8.3% rps (493.5 vs 455.5), -23% median latency (68.5 vs 88.6 ms), -7% p95; memory parity (287 MiB both, as expected - heap behavior is runtime-, not classfile-, dominated). LEO CDP admin worker, the only clean 3/3 sweep in the whole study. Magnitude is near the quiet-host noise band (+/-5-8%) so treat ~8% cautiously, but direction never reversed. Takeaway: once you commit to a modern JVM runtime, also raising the bytecode target is free-to-positive - there is no perf reason to keep emitting old class files for compatibility you no longer need.

## Related

- [[JDK 25 runtime cut LEO CDP container RSS by 30 percent vs JDK 11 - same bytecode]]
