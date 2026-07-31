---
title: "Find then adversarial-refute verify pass cuts AI reviewer false positives"
created: 2026-07-19
type: lesson
status: seedling
source: "Vinnstack QA Cockpit 2026-07-19"
tags: [llm, ai-review, prompting, verification, vinnstack]
---

# Find then adversarial-refute verify pass cuts AI reviewer false positives

When an LLM generates *judgments* (code-review findings, coverage gaps, bug reports), the failure mode that destroys trust is the plausible-but-wrong finding. A cheap, effective guard: split generation into two passes.

1. **Find** — one call produces candidate findings (structured JSON: file, line, severity, issue, fix).
2. **Adversarial verify** — a SECOND call is told to *try to REFUTE* each finding: is it actually wrong, already handled elsewhere, a false positive, unsupported by the evidence? Keep only findings that survive. Bias the skeptic to DROP when in doubt ("a false positive costs more than a missed nit").

The verify pass gets the SAME source material (the diff/log) plus the list of findings-by-index, and returns the indices to keep. Two calls total is enough to catch most hallucinated findings; per-finding parallel skeptics (N calls, majority vote) is the heavier variant when precision matters more than cost.

Why it works: generation optimizes for recall (find everything), so it over-reports; a dedicated refutation step optimizes for precision on that candidate set. Asking the same model to "double-check" in ONE pass does not work nearly as well — the adversarial FRAMING and the separate context are what matter.

Applied in Vinnstacks AI PR review gate (lib/bdd/prReview.ts): find correctness/security/complexity issues, then an adversarial pass drops the indefensible ones before a pass|caution|block verdict. Pairs with "AI proposes, humans approve" — the survivors are advisory, never auto-merged.

Related: [[Append-only QA signal bus with DISTINCT ON latest-per-dimension decouples quality dashboards from raw tables]].

## Related

- [[Append-only QA signal bus with DISTINCT ON latest-per-dimension decouples quality dashboards from raw tables]]
