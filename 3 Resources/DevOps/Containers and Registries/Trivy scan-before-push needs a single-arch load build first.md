---
title: "Trivy scan-before-push needs a single-arch load build first"
created: 2026-06-04
type: lesson
status: seedling
source: "session 2026-06-04, LEO CDP CI pipeline"
tags: [github-actions, docker, buildx, trivy, security, gotcha]
---

# Trivy scan-before-push needs a single-arch load build first

To run a vulnerability scan (e.g. Trivy) on a Docker image **before** publishing it, you must build a **single-arch** image with `load: true` first — a multi-arch `buildx` image cannot be loaded into the local Docker daemon, so `docker/build-push-action` with `load: true` only works for one platform.

**Pattern (two build steps):**
1. Build `linux/amd64` with `load: true` → local `:scan` tag, writing the buildx layer cache (`cache-to: type=local`).
2. Run Trivy against `:scan` as a **gate** — `exit-code: '1'`, `severity: 'CRITICAL,HIGH'`. Pipeline fails here if vulnerable.
3. Second `build-push-action` builds the real (possibly multi-arch) image and pushes. It is cheap because it reuses the cache from step 1 (`cache-from: type=local`).

**Why:** lets the pipeline fail on vulnerabilities before anything ever reaches the registry, while still publishing multi-arch. The cost is two build steps, mitigated by the shared local layer cache.

Learned while building the LEO CDP `release.yml`; the two-build-step ordering exists specifically because of the multi-arch/load incompatibility.

## Related

- [[GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern]]
