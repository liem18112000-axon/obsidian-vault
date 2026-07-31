---
title: "Run mvn test-compile after changing a record/ctor signature — Cloud Build compiles tests, local mvn compile does not"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [java, maven, cloud-build, records, testing, gotcha, luz-docs]
---

# Run mvn test-compile after changing a record/ctor signature — Cloud Build compiles tests, local mvn compile does not

luz-docs Cloud Build (build-maven-project step) compiles AND runs the test sources; a local 'mvn compile' only does main sources. So changing a shared record/constructor signature (e.g. adding a field to MaterializeState or MaterializeInput) compiles locally but FAILS the Cloud Build at test-compile because existing *Test.java files still call the old arity. Cost me a full ~8-min build cycle to discover.

Rule: after any signature change to a type used by tests, run 'mvn -q test-compile' (or 'mvn test') locally BEFORE shipping — not just 'mvn compile'. Fix the existing tests in the same change (add the new arg; a random/irrelevant field can take a dummy like 0 or null since the assertions check individual getters, not full-record equality).

Adjacent: when a record gains a field that is random/non-deterministic (_shard), check tests don't assert full-record equals()/toString() — those would break; per-getter assertions are safe.

## Related

- [[1 Projects/luz-docs/materialize/Partition the materialized count on a uniform _countShard int, not _id]]
