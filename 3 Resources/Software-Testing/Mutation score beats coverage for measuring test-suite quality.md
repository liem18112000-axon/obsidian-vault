---
title: "Mutation score beats coverage for measuring test-suite quality"
created: 2026-09-03
type: concept
status: seedling
source: "deep research 2026-09-03"
tags: [testing, mutation-testing, coverage, quality]
---

# Mutation score beats coverage for measuring test-suite quality

Line/branch **coverage measures what a test *executes*, not what it *checks*** — a suite can reach 100% coverage while asserting almost nothing (documented cases of ~100% coverage / ~4% mutation score). **Mutation testing** gives the missing quality signal: seed many small synthetic faults ("mutants") into the code and run the suite against each; a test is only "good" if it **kills** mutants (fails on the faulty version). The **mutation score** = fraction of mutants killed.

**Why it matters:** use coverage as the *cheap* signal that guides a generation loop, but certify final suite quality with mutation score. You can also close a loop: feed *surviving* mutants back to the generator so it writes tests that kill them (MuTAP/MUTGEN), and target fault classes tied to the risk area (Meta ACH). Engines: **mutmut** (Python), **PIT** (Java), **Stryker** (JS). Complements [[Assured test generation: keep an LLM test only if it builds, passes, and raises coverage]].

## Related

- [[Assured test generation: keep an LLM test only if it builds]]
- [[passes]]
- [[and raises coverage]]
