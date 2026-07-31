---
ai_hash: 2a35566e9e2bdfce
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04 fb-info-project sign-license.yml
status: seedling
tags:
- github-actions
- gh-cli
- workflow-dispatch
- gotcha
title: workflow_dispatch Run button only appears on the default branch - use gh workflow
  run --ref to dispatch from a feature branch
type: lesson
---

# workflow_dispatch Run button only appears on the default branch - use gh workflow run --ref to dispatch from a feature branch

A GitHub Actions `workflow_dispatch` workflow only shows its **Run workflow** button in the Actions UI once the workflow YAML exists on the repo's DEFAULT branch. A brand-new dispatch workflow living only on a feature branch is invisible in the UI - a common "I added it but can't run it" confusion.

You do NOT have to merge first to test it: the CLI dispatches against any ref, using the workflow definition from that ref:

```
gh workflow run <file.yml> --ref <branch> -f key=value -f key2=value2
gh run list --workflow=<file.yml>   # find the run id
gh run watch <run-id>               # follow to completion
gh run download <run-id> -n <artifact-name>
```

`-f` passes `workflow_dispatch` inputs. So the loop is: dispatch from the branch via CLI to validate, then merge so the UI button appears for routine use.

Related: [[Deliver a CI-minted credential via a masked short-retention artifact, not the run log]], [[CI token-signing workflow - verify the secret private key matches the pinned public key before signing]]

%% ai-graph-start %%

**Related notes:**
- [[Scheduled GitHub Actions only run on the default branch; self-closing reminder issue pattern]]
- [[GitHub Actions in a monorepo workflows live at repo root, scope per project with paths filters]]
- [[secrets context is not available in GitHub Actions if conditions]]
- [[GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]]
- [[Publish a Docker image to GHCR from GitHub Actions with GITHUB_TOKEN]]

%% ai-graph-end %%