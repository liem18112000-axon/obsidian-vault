---
title: "luz_docs_integration_test AI pipeline branch and PR mechanics"
created: 2026-07-11
type: concept
status: seedling
source: "vinnstack session 2026-07-11: building implement-bdd-steps skill"
tags: [bdd, git, bitbucket, branch-naming, luz-docs-integration-test]
---

# luz_docs_integration_test AI pipeline branch and PR mechanics

In `luz_docs_integration_test`, the existing `helpers/ai/bitbucket.py` module defines how the AI pipeline ships generated test artifacts:

- **Branch naming**: `make_branch_name(issue_key)` returns `f"{AI_BRANCH_PREFIX}/{issue_key.lower()}/{timestamp}"` — default prefix `kepler/test` (overridable via `AI_BRANCH_PREFIX` env var / Cloud Build substitution `_AI_BRANCH_PREFIX`). This is an AI-tooling convention, distinct from the human convention observed on the same repo (`git branch -a`), which is `kepler/sprint-<N>/<JIRA-KEY>-<slug>` (e.g. `kepler/sprint-158/luz-155518-adapt-it-materialize`).
- **Commit scope is silently filtered**: `commit_files()` only stages paths under `features/` — anything outside that directory is skipped with a printed warning, never committed. Any tooling built to inject generated tests/steps into this repo must assume this same scope restriction (or replicate it deliberately) so a run does not silently drop non-`features/` files it meant to include.
- **PR creation**: one PR per run, opened via the BitBucket Cloud REST API (`POST /2.0/repositories/{workspace}/{repo_slug}/pullrequests`), bundling every changed file from that branch.
- Git identity used by the automation: `user.name = "AI Agent"`, `user.email = <BITBUCKET_USERNAME>`, credentials wired through a git credential helper built from `BITBUCKET_USERNAME` + `BITBUCKET_API_TOKEN`.

Any new pipeline that ships into this repo under a *different* branch-naming rule (e.g. vinnstack's `mt-receive/LUZ-XXX-brief-name` convention) must explicitly override `make_branch_name`'s behavior rather than reuse it as-is — the default prefix is baked into that function, not just an env default that happens to differ.

Related: [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]

## Related

- [[3 Resources/Work-Kepler/luz-docs/integration-test/luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]
