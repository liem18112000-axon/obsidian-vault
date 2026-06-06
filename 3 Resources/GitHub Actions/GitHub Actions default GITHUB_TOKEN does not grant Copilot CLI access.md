---
title: "GitHub Actions default GITHUB_TOKEN does not grant Copilot CLI access"
created: 2026-06-05
type: gotcha
status: seedling
source: "session 2026-06-05 leo-agentic-notebook"
tags: [github-actions, copilot, auth, gotcha]
---

# GitHub Actions default GITHUB_TOKEN does not grant Copilot CLI access

The automatic `secrets.GITHUB_TOKEN` provided to every GitHub Actions job does **not** grant access to GitHub Copilot. A workflow step that runs the Copilot CLI (`copilot -p ...`) will fail to authenticate against Copilot using only `GITHUB_TOKEN`.

Fix: supply a Personal Access Token belonging to an account that has an active Copilot subscription, e.g. as a `COPILOT_CLI_TOKEN` repo secret, and expose it to the step. Note these are two *separate* tokens with different jobs:

- `GH_TOKEN` / `GITHUB_TOKEN` — used by the `gh` CLI to read and post on issues/PRs (the built-in token is fine for this).
- `COPILOT_CLI_TOKEN` (Copilot-enabled PAT) — used by the Copilot CLI itself for model access.

This is a common stumbling block because most GitHub Actions integrations work out of the box with the built-in token; Copilot is an exception.

See [[Run GitHub Copilot CLI in a GitHub Actions workflow]].

## Related

- [[Run GitHub Copilot CLI in a GitHub Actions workflow]]
