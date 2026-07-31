---
ai_hash: 0b6bf046fbb3c738
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05 leo-agentic-notebook
status: seedling
tags:
- github-actions
- copilot
- ci
- migration
title: Run GitHub Copilot CLI in a GitHub Actions workflow
type: howto
---

# Run GitHub Copilot CLI in a GitHub Actions workflow

There is no 1:1 drop-in "GitHub Copilot action" equivalent to the Anthropic Claude Code action (`anthropics/claude-code-action@v1`). To run Copilot in a workflow, install the CLI and invoke it with a prompt in a `run` step — which closely mirrors the prompt-driven Claude step it replaces:

```yaml
- name: Set up Node.js
  uses: actions/setup-node@v4
  with:
    node-version: "22"
- name: Install GitHub Copilot CLI
  run: npm install -g @github/copilot
- name: Run Copilot
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}          # for the gh CLI
    COPILOT_CLI_TOKEN: ${{ secrets.COPILOT_CLI_TOKEN }}  # Copilot-enabled PAT
  run: |
    copilot --allow-all-tools -p "Review PR #${{ github.event.pull_request.number }} ..."
```

`--allow-all-tools` is what makes it non-interactive (auto-approves tool execution) — essential in CI. When migrating the mention-triggered Claude workflow, the trigger convention also flips: `@claude` becomes `@copilot` in the `contains(github.event.comment.body, ...)` conditions.

Alternatives considered for the same goal: requesting Copilot as a PR reviewer via the `gh` CLI (the `copilot-pull-request-reviewer` bot — GitHubs native Copilot code-review product), or the official `actions/ai-inference` action running prompts against GitHub Models. The CLI route was chosen here because it most directly maps to the existing prompt-based steps.

See [[GitHub Actions default GITHUB_TOKEN does not grant Copilot CLI access]] for the auth gotcha.

## Related

- [[GitHub Actions default GITHUB_TOKEN does not grant Copilot CLI access]]

%% ai-graph-start %%

**Related notes:**
- [[GitHub Copilot code review is a native PR reviewer, not a workflow job]]
- [[GitHub Actions default GITHUB_TOKEN does not grant Copilot CLI access]]
- [[Google ships an official Gemini CLI GitHub Action for PR review and mentions]]
- [[Re-triggering GitHub Copilot PR review via API and its quota-limit gotcha]]
- [[Claude Code headless auth setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN]]

%% ai-graph-end %%