---
ai_hash: 4b2e18d439a55029
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: leo-cdp-framework ci-cd.yml debugging 2026-06-06
status: seedling
tags:
- github-actions
- services
- containers
- ci
- gotcha
title: Job-level defaults.run.working-directory breaks Initialize containers (pre-checkout)
type: lesson
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

- [[1 Projects/leo-cdp/framework/LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

%% ai-graph-start %%

**Related notes:**
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
- [[Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[GitHub Actions continue-on-error step-level goes green, job-level stays red]]
- [[Same-repo branch push fires both push and pull_request events (duplicate CI runs)]]

%% ai-graph-end %%