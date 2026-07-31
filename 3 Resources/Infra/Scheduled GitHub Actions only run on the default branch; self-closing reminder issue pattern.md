---
title: "Scheduled GitHub Actions only run on the default branch; self-closing reminder issue pattern"
created: 2026-06-14
type: lesson
status: seedling
source: "session 2026-06-14, accesstrade_integration key-rotation-reminder"
tags: [github-actions, cron, ci-cd, automation, gh-cli]
---

# Scheduled GitHub Actions only run on the default branch; self-closing reminder issue pattern

**GitHub Actions `schedule` (cron) triggers only fire from the workflow file on the DEFAULT branch (e.g. main). A scheduled workflow committed to a feature branch will NOT run on its timer until merged.** Always add `workflow_dispatch` so you can test it from any branch via 'Run workflow'.

Useful companion pattern — a **self-closing reminder issue** for a recurring 'you should fix X' nag, so it's visible and auto-resolves:
- The job detects state (e.g. secret presence: `env: WIF: ${{ secrets.GCP_WIF_PROVIDER }}` then `[ -n "$WIF" ]`; secrets are usable in `env`/`run`, not in job-level `if`).
- While the bad state holds: find an existing open issue by title (`gh issue list --search 'TITLE in:title' --json number --jq '.[0].number // empty'`); create it if absent, else add a dated reminder comment. Needs `permissions: issues: write` and `GH_TOKEN: ${{ github.token }}`.
- When the good state is reached: close that issue with a comment. → the nag is created once, refreshed while relevant, and disappears on its own.
- Surface a human-readable status via `>> "$GITHUB_STEP_SUMMARY"` and `::warning::` annotations.

Build issue bodies with `echo ... > file` + `gh issue create --body-file`, NOT a heredoc — an indented closing `EOF` inside a YAML `run: |` block won't terminate the heredoc. Relates to [[Gate a GitHub Actions job on secret presence via a preflight job output]].
