---
title: "Service takeover order: scan, reproduce, measure, stabilize, improve"
created: 2026-08-31
type: lesson
status: seedling
source: "session 2026-08-24 ecomart-java takeover playbook"
tags: [support-engineering, onboarding, sre, operations, methodology]
---

# Service takeover order: scan, reproduce, measure, stabilize, improve

When taking over an unfamiliar service + codebase, follow a fixed order — doing steps out of order is how support engineers make things worse:

1. **Scan before you run.** Handed-over code runs with your privileges; do a dangerous-API + secrets + build-wrapper-integrity pass before building it.
2. **Reproduce before you fix.** Get a green build on a clean machine with only the documented prereqs. No reproducible build → no safety net. Onboarding friction predicts incident friction.
3. **Measure before you touch.** Capture a baseline (build/test pass rate, coverage, contract adherence, dep CVEs, and runtime SLIs + DORA metrics if available). The "before" number is what makes your improvement provable.
4. **Stabilize before you optimize.** Fix build-breakers and contract-breaking bugs with the smallest reversible change + a regression test each, keeping the suite green — *before* any refactor.
5. **Improve + operationalize.** Then harden latent bugs, add observability (logs w/ correlation IDs, health endpoint, SLO alerts), CI gates, runbook, and kill the bus factor.

Key heuristics: green tests can pass for the wrong reason (ask "would this fail if the behaviour were wrong?"); MTTR is dominated by detect+understand, not fix; mitigate to stop the bleeding but always finish the root-cause fix; communicate trade-offs, don't just make them.
