---
ai_hash: 421607911b5bd55b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11
status: seedling
tags:
- git
- branching
- gotcha
- luz-kubernetes
title: Branch created from current HEAD drags unrelated commits — verify against origin/master
type: lesson
---

# Branch created from current HEAD drags unrelated commits — verify against origin/master

When a script or skill creates a feature branch with plain `git checkout -b <name>` (no start point), the branch forks from the *current HEAD* — if the repo happened to sit on another feature branch, every commit of that branch silently rides along into the "new" branch and its PR diff.

Seen in luz_kubernetes: `add-env-props-luz-docs-materialized` was cut while the repo sat on the sprint-153 luz-enricher branch, so the PR vs master included unrelated LUZ-151596 commits (env.sh pub/sub config, k8s manifests). Cleanup = `git checkout origin/master -- <files>` + commit for stray files, or rebase onto master for full hygiene.

Rule: right after creating a branch for an isolated change, run `git log origin/master..HEAD --oneline` / `git diff origin/master...HEAD --stat` and confirm only your intended commits/files appear. Branch-creating automation should use `git checkout -b <name> origin/master` when the change is master-based.

%% ai-graph-start %%

**Related notes:**
- [[Apply one feature from a stale branch without reverting newer work (checkout ref -- paths)]]
- [[luz_docs_integration_test AI pipeline branch and PR mechanics]]
- [[Pre-staged files silently merge selective commit batches - check the index first]]
- [[Re-verify file state before trusting findings on long-running reviews]]
- [[eArchive PRs in luz_docs target earchive-master integration branch, not master]]

%% ai-graph-end %%