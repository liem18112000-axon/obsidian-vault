---
title: "Job-level defaults.run.working-directory breaks Initialize containers (pre-checkout)"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml debugging 2026-06-06"
tags: [github-actions, services, containers, ci, gotcha]
---

# Job-level defaults.run.working-directory breaks Initialize containers (pre-checkout)

Setting `defaults.run.working-directory` at the **job** level in a GitHub Actions workflow that also declares service `containers:` makes the run fail at the **"Initialize containers"** pre-step with:

```
An error occurred trying to start process "/usr/bin/bash" with working directory ".../<dir>". No such file or directory
One or more containers failed to start.
```

**Why:** service containers (and their setup shell) initialize **before** `actions/checkout`, so the working directory does not exist yet. The job default is applied to that pre-step too. Any step with `if: always()` (e.g. a summary step) also fails the same way because the dir is never created when checkout is skipped.

**Fix:** do not set a job-level default working-directory alongside `services:`. Instead set `working-directory:` on each individual `run:` step that needs it (those run after checkout).

## Related
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

## Related

- [[LEO CDP CI provisions deps CI-natively]]
- [[pinned to devops-script versions for parity]]
