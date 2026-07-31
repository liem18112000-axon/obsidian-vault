---
title: "Same-repo branch push fires both push and pull_request events (duplicate CI runs)"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml 2026-06-06"
tags: [github-actions, ci, pull-request, duplicate-runs, gotcha]
---

# Same-repo branch push fires both push and pull_request events (duplicate CI runs)

When a branch in the SAME repo has an open PR, pushing a commit fires **two** GitHub Actions runs for the same SHA: a `push` event and a `pull_request` (synchronize) event. If a job behaves differently per event — e.g. build+push on `push`, build-only on `pull_request` — you get two runs that look contradictory. The **PR "Checks" tab surfaces the `pull_request` run**, so a build-only PR run reads as "CI builds but never pushes the image," even though the parallel `push` run did push it.

**Diagnosis tip:** filter runs by event — `GET /repos/{owner}/{repo}/actions/runs?event=push` vs `?event=pull_request` — to see which run did what for a given SHA. Confirm a push actually happened with `docker manifest inspect <ref>` (works without auth if the package is public).

**Dedupe options:**
- Skip the job on same-repo PRs (the push run already covers that commit), keep it for fork PRs (which get NO push event):
  ```yaml
  if: github.event_name == push ||
      (github.event_name == pull_request && github.event.pull_request.head.repo.full_name != github.repository)
  ```
- Or a `concurrency` group — but push and PR events have different `github.ref` (`refs/heads/x` vs `refs/pull/N/merge`), so a ref-keyed group will NOT cancel one against the other. Key on `github.event.pull_request.head.sha || github.sha` if you want cross-event dedup.

## Related
- [[CI: build Docker image on every run, push only on non-PR]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

## Related

- [[CI: build Docker image on every run]]
- [[push only on non-PR]]
- [[LEO CDP CI provisions deps CI-natively]]
- [[pinned to devops-script versions for parity]]
