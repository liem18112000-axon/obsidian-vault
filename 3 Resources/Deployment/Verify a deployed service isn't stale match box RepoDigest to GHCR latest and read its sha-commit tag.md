---
title: "Verify a deployed service isn't stale: match box RepoDigest to GHCR :latest and read its sha-<commit> tag"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 deployments, session 2026-08-23"
tags: [cd, docker, ghcr, image-tags, verification, leo-customer360]
---

# Verify a deployed service isn't stale: match box RepoDigest to GHCR :latest and read its sha-<commit> tag

**"CD deployed but it looks like the old image" — how to actually check, and why it's usually an illusion.**

Symptom: you run CD and suspect a service (e.g. backend-system/Dagster) is still running old code.

**Verify authoritatively** (leo-customer360, deploy-by-:latest):
1. Running digest on the box: `docker inspect <name> --format '{{index .RepoDigests 0}}'` -> `...@sha256:XXXX`.
2. GHCR current :latest digest + its tags: `gh api /orgs/<ORG>/packages/container/<repo>%2F<service>/versions --jq '.[0]'`. The newest version shows `tags: ["sha-<commit>", "latest"]`.
3. If box digest == GHCR :latest digest -> the box IS current. The `sha-<commit>` tag on that digest tells you EXACTLY which git commit is running (map image->commit without guessing).
Also compare the image BUILD time (`docker inspect <img> --format '{{.Created}}'`) to your last commit that touched that service's dir.

**Why it feels stale but usually isn't:**
- CD's default service list redeploys EVERY app (incl. backend) on every run, even ones that didn't change -> it looks like 'I deployed backend' but it just re-pulled the same unchanged `:latest` (a no-op image-wise).
- CI rebuilds a service's image ONLY when files under that service's dir changed (path filter) — or on a version tag (builds all). So an image legitimately stays put (and old-dated) across many deploys until that service's code changes. Old build DATE != stale relative to source.

**Real staleness risks to design against:**
- Deploying a MUTABLE `:latest` (no digest/sha pinning) hides WHAT is running. Prefer immutable `sha-<commit>` / version tags, or resolve `:latest`->@sha256 at deploy (leo repo: RESOLVE_DIGEST=1 in lib/ghcr.sh).
- A behavior change made OUTSIDE the service's dir (shared code/deps) won't trip the path filter -> no rebuild -> genuinely stale.
- Leftover LOCALLY-built `<service>:latest` from a BUILD_LOCAL deploy sits alongside the GHCR image; a build-on-box deploy uses `docker build` (layer cache) and runs the local tag — a separate staleness path. Clean up stray local images.

Source: leo-customer360 deployments (backend-system image audit), 2026-08.

## Related

- [[docker restart does not re-read --env-file; recreate the container to apply env changes]]
