---
title: "Keep the release gate deterministic; put AI judgment in upstream signals not the gate"
created: 2026-07-20
type: argument
status: seedling
source: "Vinnstack QA Cockpit 2026-07-20"
tags: [qa, release-gate, determinism, architecture, vinnstack]
---

# Keep the release gate deterministic; put AI judgment in upstream signals not the gate

A release/quality GATE (the go / caution / no-go decision) should be deterministic and LLM-free, even in an AI-heavy pipeline. Put the AI *judgment* in the UPSTREAM signals it aggregates (coverage analysis, PR-review findings, failure triage, testability score), and let the gate itself apply plain, readable rules over those signals.

Why: a release decision must be **explainable and reproducible**. Stakeholders need "no-go because 2 scenarios are failing and 3 acceptance criteria are uncovered", not "the model felt it wasnt ready". A deterministic gate gives the same verdict for the same inputs, can be audited, and its thresholds can be tuned without prompt-engineering. Non-determinism in the pass/fail path is exactly what you do NOT want at the release boundary.

Shape that works: read a normalized signal store (see [[Append-only QA signal bus with DISTINCT ON latest-per-dimension decouples quality dashboards from raw tables]]), classify each contributing signal as blocker vs warning, then: any blocker → no-go; else any warning → caution; else go. A simple readiness score (start 100, subtract per blocker/warning) gives a sortable headline number. List the reasons, blockers first.

This mirrors the broader principle "determinism where it counts": AI adds judgment on top, never in the pass/fail path. Applied in Vinnstacks release quality gate (lib/qa/releaseGate.ts).

Related: [[Find then adversarial-refute verify pass cuts AI reviewer false positives]] (that is where the AI judgment is spent instead).

## Related

- [[Append-only QA signal bus with DISTINCT ON latest-per-dimension decouples quality dashboards from raw tables]]
- [[Find then adversarial-refute verify pass cuts AI reviewer false positives]]
