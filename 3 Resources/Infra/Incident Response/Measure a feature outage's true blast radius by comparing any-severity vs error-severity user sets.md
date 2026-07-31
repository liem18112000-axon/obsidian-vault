---
title: "Measure a feature outage's true blast radius by comparing any-severity vs error-severity user sets"
created: 2026-07-16
type: howto
status: seedling
source: "session 2026-07-16 Klara Widget Store PROD incident"
tags: [incident-response, observability, logging, triage]
---

# Measure a feature outage's true blast radius by comparing any-severity vs error-severity user sets

When a feature errors in prod, the users you see in the ERROR logs are only those who **tried** the feature during the window — not the full set of affected users. To estimate the real blast radius and failure rate, compare two distinct-user sets over the same window:

- **denominator** — distinct users hitting the feature's handler/route tag at *any* severity (they attempted it)
- **numerator** — distinct users hitting it at ERROR severity (they failed)

The error list is a rolling *sample* of attempters; the true affected population is "everyone who uses the feature" and grows as more people try. The numerator/denominator ratio tells you whether it is a **total** outage (~100% fail) or **partial/intermittent** (some succeed).

Verify the "succeeded" set is real: take users who opened the feature but are absent from the error set, and confirm they have *zero* errors anywhere in the window (they might have failed under a different handler tag). Only then are they genuine successes.

Concrete: Klara Widget Store — 41 users opened it in 60m, 26 errored (~63%), 15 verified clean → a partial failure, not total, and the 26 were just this hour's attempters.

Gotcha: the window is rolling, so "right now" shifts on re-run; and the count is a floor (some failures surface only under a shared/parent handler tag without the feature's own string).

## Related

- [[Per-pod breakdown of rejections separates a bad replica from a global config problem]]
- [[Klara PROD log access]]
