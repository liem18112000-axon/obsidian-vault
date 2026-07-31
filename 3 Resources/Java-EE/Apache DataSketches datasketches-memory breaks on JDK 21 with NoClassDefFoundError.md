---
title: "Apache DataSketches datasketches-memory breaks on JDK 21 with NoClassDefFoundError"
created: 2026-07-09
type: lesson
status: seedling
source: "luz_docs foldercount HLL implementation, 2026-07-09 — hit at runtime on JDK 21.0.9, fixed by hand-rolling HLL instead"
tags: [datasketches, hyperloglog, jdk-compatibility, gotcha, java]
---

# Apache DataSketches datasketches-memory breaks on JDK 21 with NoClassDefFoundError

Adding org.apache.datasketches:datasketches-java (v6.1.0 at time of writing) as a dependency and calling HllSketch.toCompactByteArray() / HllSketch.heapify(Memory.wrap(bytes)) fails at runtime on JDK 21 with:

  java.lang.NoClassDefFoundError: Could not initialize class org.apache.datasketches.memory.internal.BaseWritableMemoryImpl
  Caused by: java.lang.ExceptionInInitializerError

This comes from the transitive datasketches-memory dependency, which does an internal Java-version compatibility check in a static initializer and has historically been broken on newer JDKs (confirmed via datasketches-memory GitHub issues: broken on 21, then on 25 too, each requiring an explicit allow-list fix upstream). The library is designed for heap AND off-heap (memory-mapped) access; even when you only ever use heap-based sketches (Memory.wrap(byte[]), no off-heap files), the version-gate in the static initializer still fires unconditionally and can crash class loading before your code path is even reached.

Practical takeaway: before depending on datasketches-java in a project, verify the exact datasketches-memory transitive version actually initializes cleanly on the JDK you both build AND deploy with (they can differ - e.g. local dev on JDK 21, container image on JDK 17) - do not assume "if it added to pom.xml and compiled, it works," since this failure only surfaces at first runtime use, not compile time. If it does not work, either pin to a datasketches-java version whose bundled datasketches-memory has the JDK-version allow-list fix for your JDK, or avoid the library entirely for simple use cases: HyperLogLog is simple enough to hand-roll in ~80 lines of pure Java (hash -> bucket -> leading-zero count -> register max, plus a linear-counting small-range correction) with zero risk of this class of platform-compatibility break. In luz_docs (docs/count-estimate feature) we ended up replacing the DataSketches dependency with exactly that hand-rolled implementation after hitting this error.

## Related

- [[HyperLogLog cardinality estimation mechanism (hash]]
- [[register]]
- [[streak-length)]]
- [[luz_docs runs non-clustered WildFly pods]]
- [[so pod-local sketch/counter state is broken]]
