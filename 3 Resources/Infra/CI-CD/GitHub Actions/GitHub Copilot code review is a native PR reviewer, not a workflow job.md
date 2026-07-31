---
ai_hash: 2dd3bb907bf63623
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: leo-cdp-framework ci-cd.yml 2026-06-06
status: seedling
tags:
- github
- copilot
- code-review
- ci
- decision
title: GitHub Copilot code review is a native PR reviewer, not a workflow job
type: lesson
---

# GitHub Copilot code review is a native PR reviewer, not a workflow job

GitHub Copilot code review is a **native GitHub feature**, not something you wire up as an Actions workflow job. Consequences:

- **No YAML, no API key, no Actions minutes.** Unlike a self-hosted AI reviewer (e.g. the `run-gemini-cli` action, which needs `GEMINI_API_KEY` + runner time and can hit free-tier 429 quota), Copilot runs on github.com.
- **Enable it two ways:** (1) per-PR — request **Copilot** in the Reviewers panel; (2) automatically — repo/org **Settings → Rules → Rulesets**, add a branch ruleset targeting `main` with "Request pull request review from Copilot."
- **Requires a Copilot license** that includes code review (Pro/Pro+/Business/Enterprise; the Free tier allows a limited number of reviews). The org/owner must have Copilot enabled for the repo.
- To check availability: open any PR and look for **Copilot** in the Reviewers list. If absent, it is a plan/settings gate, not code.

Decision context: in leo-cdp-framework we removed the Gemini review/assistant jobs (and their `pull_request_target`/`issue_comment`/... triggers and `pull-requests`/`issues` permissions) and rely on Copilot for AI review — simpler workflow, one less secret, no quota failures.

## Related
- [[3 Resources/Infra/CI-CD/GitHub Actions/GitHub Actions continue-on-error step-level goes green, job-level stays red]]
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

## Related

- [[3 Resources/Infra/CI-CD/GitHub Actions/GitHub Actions continue-on-error step-level goes green, job-level stays red]]
- [[1 Projects/leo-cdp/framework/LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

%% ai-graph-start %%

**Related notes:**
- [[Google ships an official Gemini CLI GitHub Action for PR review and mentions]]
- [[Run GitHub Copilot CLI in a GitHub Actions workflow]]
- [[Re-triggering GitHub Copilot PR review via API and its quota-limit gotcha]]
- [[GitHub Actions continue-on-error step-level goes green, job-level stays red]]
- [[GitHub Actions default GITHUB_TOKEN does not grant Copilot CLI access]]

%% ai-graph-end %%