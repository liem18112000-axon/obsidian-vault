---
title: "Finding intentional k8s config PRs in luz_kubernetes: filter out image-hash + merge drift"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18"
tags: [luz-kubernetes, bitbucket, git, kubernetes, gotcha, monorepo]
---

# Finding intentional k8s config PRs in luz_kubernetes: filter out image-hash + merge drift

When hunting for PRs that **intentionally** changed a service's Kubernetes config in the `luz_kubernetes` Bitbucket monorepo, `git diff <mergeCommit>^1 <mergeCommit>^2 -- kubernetes/<svc>/*` massively over-reports. Two causes:

1. **Image-hash sweep** — `kubernetes/<svc>/k8s.yaml` embeds the deploy image `sha256`/tag, and release tooling rewrites those hashes on nearly every merge. So almost *every* PR appears to "touch" *every* service.
2. **Merge-base drift** — `merge^1..merge^2` shows the delta the PR branch needed to reconcile against master at merge time, not what the author edited. A PR branched from an older tree surfaces unrelated drift. Tell-tale sign: the **same diff appears across unrelated PRs**, often as mirror-image `+`/`-` (e.g. `storage: 50Gi↔16Gi`, `subPathExpr` blocks jumping between bank-integration, runtime-config, and docs-import PRs). Those are reconciliation artifacts, not intent.

**Technique:** filter the `^1..^2` diff to real content lines, dropping anything matching `image:|sha256:|@sha256`; if nothing remains, the PR only bumped the image hash. Then discard identical diffs that recur across unrelated branches as drift artifacts. What survives is genuine config intent.

Caveat: this only recovers **merged** PRs (via the `Merged in <branch> (pull request #N)` commit subjects). Open/declined PRs are invisible to git — see [[Bitbucket cached git token 401s on REST API; PR listing needs app password]].

## Related

- [[Bitbucket cached git token 401s on REST API; PR listing needs app password]]
