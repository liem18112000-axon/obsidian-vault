---
title: "A resume must not re-charge one-time accounting"
created: 2026-06-15
type: lesson
status: seedling
source: "fb-info-project pause/resume, 2026-06-15"
tags: [resume, idempotency, billing, quota, resilience]
---

# A resume must not re-charge one-time accounting

When you add resume/restart to a job that has **side-effecting accounting** (usage quotas, billing counters, 'runs used'), audit every counter for whether re-running double-charges it. A resume re-invokes the same command, so any one-time-per-invocation charge fires twice unless guarded.

**The split that matters:**
- *Per-item accounting* usually composes safely with resume on its own: charge an item exactly when it completes, then mark it done and skip it. Completed items are never revisited, so they are never re-charged.
- *Per-invocation accounting* (commit `runs += 1` at startup) does NOT compose: each resume is a fresh process that would bump it again. Fix: persist a `run_committed` flag in the checkpoint and skip the startup commit when resuming.

**Concrete case (fb-info-project license):** the model commits `runs=1` up front (so killing the process can't dodge the run quota) and commits `profiles=N` per link at link completion. Adding resume only required guarding the run commit with a persisted flag; the per-profile commits already composed because a link commits once at completion and is then marked done+skipped.

Rule of thumb: idempotency under resume is per-counter — verify each, don't assume.

Related: [[A persisted dedup cache doubles as a resume log]], [[3 Resources/Practices/Software Design/Checkpoint files atomic tmp+rename write plus an input fingerprint]]

## Related

- [[A persisted dedup cache doubles as a resume log]]
- [[3 Resources/Practices/Software Design/Checkpoint files atomic tmp+rename write plus an input fingerprint]]
