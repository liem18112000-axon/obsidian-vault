---
name: gemini-cli-yolo-approval-mode-ci
description: How to stop Gemini CLI blocking on tool-confirmation prompts in CI (approvalMode yolo)
metadata:
  type: reference
---

The Gemini CLI pauses on `Do you want to continue? [Y/n]:` before each tool call. In a non-interactive CI job (e.g. the `google-github-actions/run-gemini-cli` action) this hangs/fails the run.

Fix: set the approval mode in `.gemini/settings.json` (the action's `settings:` input becomes this file):

- `"approvalMode": "yolo"` — auto-approve **all** tool calls. Needed when the agent uses MCP tools (e.g. GitHub MCP posting review comments), because lesser modes still prompt for those.
- `"auto_edit"` — only auto-approves file edits (write/replace), still prompts for everything else.
- `"default"` — prompts on every call. `"plan"` — read-only.

Note: some docs claim YOLO is CLI-flag-only (`--yolo` / `--approval-mode=yolo`), but `approvalMode: "yolo"` in settings.json is accepted and is the clean way to do it in an Action where you can't pass CLI flags.

Used in this repo's `.github/workflows/gemini-code-review.yml` and `gemini.yml`. Also pair with env `GEMINI_CLI_TRUST_WORKSPACE: 'true'`.
