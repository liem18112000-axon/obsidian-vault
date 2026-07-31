---
title: "Branch created from current HEAD drags unrelated commits — verify against origin/master"
created: 2026-06-11
type: lesson
status: seedling
source: "session 2026-06-11"
tags: [git, branching, gotcha, luz-kubernetes]
---

# Branch created from current HEAD drags unrelated commits — verify against origin/master

When a script or skill creates a feature branch with plain `git checkout -b <name>` (no start point), the branch forks from the *current HEAD* — if the repo happened to sit on another feature branch, every commit of that branch silently rides along into the "new" branch and its PR diff.

Seen in luz_kubernetes: `add-env-props-luz-docs-materialized` was cut while the repo sat on the sprint-153 luz-enricher branch, so the PR vs master included unrelated LUZ-151596 commits (env.sh pub/sub config, k8s manifests). Cleanup = `git checkout origin/master -- <files>` + commit for stray files, or rebase onto master for full hygiene.

Rule: right after creating a branch for an isolated change, run `git log origin/master..HEAD --oneline` / `git diff origin/master...HEAD --stat` and confirm only your intended commits/files appear. Branch-creating automation should use `git checkout -b <name> origin/master` when the change is master-based.
