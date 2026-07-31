---
title: Persist the guard before the side effect for at-most-once
tags: [reliability, crash-safety, idempotency, design-decision]
created: 2026-06-16
---

# Persist the guard before the side effect for at-most-once

When a "do this once across crashes/resumes" guard wraps an irreversible side effect (charging a quota, sending a charge, emitting an event), **the order of the two persisted writes decides the crash-window failure mode**:

- **side-effect first, then mark guard** → crash in the gap = side effect happened but guard not set → resume **re-does** it → *at-least-once* (double-charge).
- **mark guard first, then side-effect** → crash in the gap = guard set but side effect skipped → resume sees the guard and skips → *at-most-once* (occasionally never happens).

Pick by which failure is worse. For a **license/quota charge**, double-billing an honest user is the worse harm, and "dodge by crashing in a sub-millisecond window every run" is not a practical exploit — so **mark the guard first**. For anti-fraud accounting where missing a charge is worse, prefer at-least-once.

Real example: `fb-info-project` `service.batch` run-commit guard — reordered to `cp.mark_run_committed()` *then* `grant.commit(runs=1)` so a crash leaves the run uncharged rather than charged-but-unmarked (PR #3 review, commit 7363119).

True exactly-once needs the guard + side effect in one atomic transaction; when they live in separate files/stores you can't get it, so you consciously choose at-most-once vs at-least-once.
