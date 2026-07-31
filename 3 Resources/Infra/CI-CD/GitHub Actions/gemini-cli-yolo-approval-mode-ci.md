---
ai_hash: 7b9419530d995624
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
description: How to stop Gemini CLI blocking on tool-confirmation prompts in CI (approvalMode
  yolo)
entities: []
metadata:
  type: reference
name: gemini-cli-yolo-approval-mode-ci
---

The Gemini CLI pauses on `Do you want to continue? [Y/n]:` before each tool call. In a non-interactive CI job (e.g. the `google-github-actions/run-gemini-cli` action) this hangs/fails the run.

Fix: set the approval mode in `.gemini/settings.json` (the action's `settings:` input becomes this file):

- `"approvalMode": "yolo"` — auto-approve **all** tool calls. Needed when the agent uses MCP tools (e.g. GitHub MCP posting review comments), because lesser modes still prompt for those.
- `"auto_edit"` — only auto-approves file edits (write/replace), still prompts for everything else.
- `"default"` — prompts on every call. `"plan"` — read-only.

Note: some docs claim YOLO is CLI-flag-only (`--yolo` / `--approval-mode=yolo`), but `approvalMode: "yolo"` in settings.json is accepted and is the clean way to do it in an Action where you can't pass CLI flags.

Used in this repo's `.github/workflows/gemini-code-review.yml` and `gemini.yml`. Also pair with env `GEMINI_CLI_TRUST_WORKSPACE: 'true'`.

%% ai-graph-start %%

**Related notes:**
- [[Google ships an official Gemini CLI GitHub Action for PR review and mentions]]
- [[GitHub Copilot code review is a native PR reviewer, not a workflow job]]
- [[GitHub Actions continue-on-error step-level goes green, job-level stays red]]
- [[Run GitHub Copilot CLI in a GitHub Actions workflow]]

%% ai-graph-end %%