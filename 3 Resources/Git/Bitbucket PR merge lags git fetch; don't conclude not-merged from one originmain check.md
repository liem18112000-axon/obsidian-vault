---
title: "Bitbucket PR merge lags git fetch; don't conclude not-merged from one origin/main check"
created: 2026-07-18
type: lesson
status: seedling
source: "Vinnstack session 2026-07-18"
tags: [git, bitbucket, ci, gotcha, vinnstack]
---

# Bitbucket PR merge lags git fetch; don't conclude not-merged from one origin/main check

A Bitbucket Cloud PR that has been merged does NOT show up on `git fetch origin main` instantly — there is propagation lag between the Bitbucket-side merge and what `origin/main` reports to a git client. In one Vinnstack session the user merged PR #5, but two successive `git fetch origin main` checks still showed the old `origin/main` tip (and the source branch still present, tip not reachable from main) — which looked exactly like "not merged yet". Minutes later the same fetch showed `origin/main` = the PR-#5 merge commit.

Lesson: do NOT conclude "the PR was not merged" from a single `git fetch` snapshot. Signals like "origin/main unchanged", "source branch still exists", "af52dec not reachable from main" can ALL be true simply due to sync lag, not a failed/blocked merge. Before diagnosing a blocked merge (approvals, checks, wrong dest), re-fetch after a short wait, or check the PR state on the Bitbucket side directly.

Related: a background merge-watcher that polls `origin/main` for advancement is the robust way to catch it — it fired correctly for PR #4 once propagation completed.

## Related

- [[Vinnstack release: push to main triggers Cloud Build which publishes to GCS latest/ auto-update channel]]
