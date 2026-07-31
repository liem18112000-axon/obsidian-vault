---
title: "Google ships an official Gemini CLI GitHub Action for PR review and mentions"
created: 2026-06-05
type: howto
status: seedling
source: "session 2026-06-05 leo-agentic-notebook"
tags: [github-actions, gemini, google-ai, ci, migration]
---

# Google ships an official Gemini CLI GitHub Action for PR review and mentions

Unlike GitHub Copilot (which has no purpose-built Action), Google ships an official, first-party GitHub Action — `google-github-actions/run-gemini-cli@v0` — that wraps the Gemini CLI for use in workflows. It is the recommended path for agentic PR review and `@gemini` mention-triggered automation, and Google publishes ready-made example workflows (PR review, issue triage, `@gemini` dispatch) around it.

Minimal usage:

```yaml
- name: Run Gemini
  uses: google-github-actions/run-gemini-cli@v0
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}   # CLI reads/posts on PRs & issues
    gemini_api_key: ${{ secrets.GEMINI_API_KEY }}
    prompt: |
      Review PR #${{ github.event.pull_request.number }} ...
```

Auth options:
- **`gemini_api_key`** — an API key from Google AI Studio (https://aistudio.google.com/apikey). Simplest.
- **Vertex AI / Workload Identity** — set `use_vertex_ai: true` plus `gcp_workload_identity_provider` / `gcp_project_id` (no long-lived key). Preferred for GCP-hosted orgs.

Contrast: the Copilot equivalent requires a raw `npm install -g @github/copilot` + `copilot -p` run step because there is no official Copilot Action. The Gemini Action is cleaner because it bundles the CLI install, auth, and GitHub tools.

Mention convention mirrors the others: trigger on `@gemini` in `contains(github.event.comment.body, ...)` conditions.

Related: [[Run GitHub Copilot CLI in a GitHub Actions workflow]].

## Related

- [[Run GitHub Copilot CLI in a GitHub Actions workflow]]
