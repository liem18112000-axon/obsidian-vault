---
ai_hash: bcd59a8bb2df5448
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: leo-cdp-framework ci-cd.yml work 2026-06-06
status: seedling
tags:
- github-actions
- ci
- continue-on-error
- gotcha
title: 'GitHub Actions continue-on-error: step-level goes green, job-level stays red'
type: lesson
---

# GitHub Actions continue-on-error: step-level goes green, job-level stays red

GitHub Actions `continue-on-error: true` behaves differently depending on where you put it, and the difference is a UX gotcha:

- **Step-level** (`jobs.<j>.steps[*].continue-on-error`): if that step fails, it is treated as **success** for the job outcome — the step shows a warning but the **job goes green**. Use this when you want a flaky/optional step (e.g. an advisory AI review that can hit a 429 / exhausted quota) to be *skipped cleanly* with no red anywhere.
- **Job-level** (`jobs.<j>.continue-on-error`): a failing job no longer fails the **overall workflow run**, but the **job itself still shows as failed (red)** in the UI.

So "make this error just skip and not look broken" = **step-level**. "Let this whole job fail without failing the run" = job-level.

Concretely: for an advisory `run-gemini-cli` step that errors on `TerminalQuotaError` (HTTP 429, free-tier daily limit), put `continue-on-error: true` on the **step** so the gemini-review/gemini jobs stay green.

## Related
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
- [[secrets context is not available in GitHub Actions if conditions]]

## Related

- [[1 Projects/leo-cdp/framework/LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
- [[secrets context is not available in GitHub Actions if conditions]]

%% ai-graph-start %%

**Related notes:**
- [[GitHub Copilot code review is a native PR reviewer, not a workflow job]]
- [[secrets context is not available in GitHub Actions if conditions]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[Job-level defaults.run.working-directory breaks Initialize containers (pre-checkout)]]
- [[gemini-cli-yolo-approval-mode-ci]]

%% ai-graph-end %%