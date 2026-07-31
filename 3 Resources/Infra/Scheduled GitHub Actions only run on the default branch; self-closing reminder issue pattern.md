---
ai_hash: 5b255d19842e2dde
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: session 2026-06-14, accesstrade_integration key-rotation-reminder
status: seedling
tags:
- github-actions
- cron
- ci-cd
- automation
- gh-cli
title: Scheduled GitHub Actions only run on the default branch; self-closing reminder
  issue pattern
type: lesson
---

# Scheduled GitHub Actions only run on the default branch; self-closing reminder issue pattern

**GitHub Actions `schedule` (cron) triggers only fire from the workflow file on the DEFAULT branch (e.g. main). A scheduled workflow committed to a feature branch will NOT run on its timer until merged.** Always add `workflow_dispatch` so you can test it from any branch via 'Run workflow'.

Useful companion pattern — a **self-closing reminder issue** for a recurring 'you should fix X' nag, so it's visible and auto-resolves:
- The job detects state (e.g. secret presence: `env: WIF: ${{ secrets.GCP_WIF_PROVIDER }}` then `[ -n "$WIF" ]`; secrets are usable in `env`/`run`, not in job-level `if`).
- While the bad state holds: find an existing open issue by title (`gh issue list --search 'TITLE in:title' --json number --jq '.[0].number // empty'`); create it if absent, else add a dated reminder comment. Needs `permissions: issues: write` and `GH_TOKEN: ${{ github.token }}`.
- When the good state is reached: close that issue with a comment. → the nag is created once, refreshed while relevant, and disappears on its own.
- Surface a human-readable status via `>> "$GITHUB_STEP_SUMMARY"` and `::warning::` annotations.

Build issue bodies with `echo ... > file` + `gh issue create --body-file`, NOT a heredoc — an indented closing `EOF` inside a YAML `run: |` block won't terminate the heredoc. Relates to [[secrets context is not available in GitHub Actions if conditions]].

%% ai-graph-start %%

**Related notes:**
- [[secrets context is not available in GitHub Actions if conditions]]
- [[workflow_dispatch Run button only appears on the default branch - use gh workflow run --ref to dispatch from a feature branch]]
- [[GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]]
- [[Set up GitHub Actions to GCP via Workload Identity Federation]]
- [[Gate Terraformdeploy CI to push-on-main, not pull_request (secrets fail PRs)]]

%% ai-graph-end %%