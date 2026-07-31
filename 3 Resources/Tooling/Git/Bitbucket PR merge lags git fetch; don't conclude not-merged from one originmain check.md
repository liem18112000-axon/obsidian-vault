---
ai_hash: 7c0b86fc73ea4647
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-18
entities: []
source: Vinnstack session 2026-07-18
status: seedling
tags:
- git
- bitbucket
- ci
- gotcha
- vinnstack
title: Bitbucket PR merge lags git fetch; don't conclude not-merged from one origin/main
  check
type: lesson
---

# Bitbucket PR merge lags git fetch; don't conclude not-merged from one origin/main check

A Bitbucket Cloud PR that has been merged does NOT show up on `git fetch origin main` instantly — there is propagation lag between the Bitbucket-side merge and what `origin/main` reports to a git client. In one Vinnstack session the user merged PR #5, but two successive `git fetch origin main` checks still showed the old `origin/main` tip (and the source branch still present, tip not reachable from main) — which looked exactly like "not merged yet". Minutes later the same fetch showed `origin/main` = the PR-#5 merge commit.

Lesson: do NOT conclude "the PR was not merged" from a single `git fetch` snapshot. Signals like "origin/main unchanged", "source branch still exists", "af52dec not reachable from main" can ALL be true simply due to sync lag, not a failed/blocked merge. Before diagnosing a blocked merge (approvals, checks, wrong dest), re-fetch after a short wait, or check the PR state on the Bitbucket side directly.

Related: a background merge-watcher that polls `origin/main` for advancement is the robust way to catch it — it fired correctly for PR #4 once propagation completed.

## Related

- [[2 Areas/Vinnstack/Vinnstack release push to main triggers Cloud Build which publishes to GCS latest auto-update channel]]

%% ai-graph-start %%

**Related notes:**
- [[FETCH_HEAD is volatile when an IDE auto-fetches]]
- [[gh CLI is GitHub-only, not Bitbucket-aware]]
- [[Bitbucket Cloud pull-request REST API shape]]
- [[Bitbucket Cloud PR comment resolution is presence-of-object, not a boolean]]

%% ai-graph-end %%