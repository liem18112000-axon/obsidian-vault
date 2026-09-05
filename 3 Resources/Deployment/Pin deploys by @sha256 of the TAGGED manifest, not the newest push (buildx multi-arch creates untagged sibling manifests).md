---
title: "Pin deploys by @sha256 of the TAGGED manifest, not the newest push (buildx multi-arch creates untagged sibling manifests)"
created: 2026-08-23
type: lesson
status: seedling
source: "leo-customer360 deployments/lib/ghcr.sh, session 2026-08-23"
tags: [docker, ghcr, digest-pinning, buildx, cd, leo-customer360]
---

# Pin deploys by @sha256 of the TAGGED manifest, not the newest push (buildx multi-arch creates untagged sibling manifests)

**Pin container deploys by @sha256 digest, and resolve the digest from the TAGGED version — not the newest push.**

Deploying a mutable tag (`:latest`) is unverifiable: you can't tell which build is live, and a box can silently keep a stale tag. Fix: resolve the tag to an immutable `@sha256` digest at deploy time and `docker pull image@sha256:...`.

**Subtle trap when resolving via the registry API:** a buildx MULTI-ARCH push creates SEVERAL package versions at the same instant — the tagged **manifest index** PLUS untagged per-arch manifests and a build **attestation** manifest. So 'take the newest version' (`versions?per_page=1 --jq '.[0].name'`) can return an UNTAGGED sub-manifest's digest and pin the wrong thing (pull may get one arch, or an attestation, or fail). Select the version that actually carries the tag instead:
```
gh api ".../packages/container/<repo>%2F<svc>/versions?per_page=100"   --jq "[.[] | select(.metadata.container.tags | index(\"\"))][0].name // empty"
```

**Make it degrade gracefully:** if the digest lookup fails (no `gh`, no package:read, tag absent), fall back to the plain `:tag` and print WHY to stderr — a deploy should never hard-fail on resolution, but the fallback must be observable (else 'pinning' silently doesn't happen).

**The immutable `sha-<commit>` tag is the other half:** CI pushing `type=sha,format=long` gives every build a `sha-<full-git-sha>` tag, so a digest maps back to an exact commit — verify with `gh api .../versions --jq '.[0].metadata.container.tags'`.

Caveat: a per-commit `sha-<sha>` tag exists ONLY for services CHANGED in that commit (path-filtered CI builds), so you can't blanket-deploy every service at one commit's sha tag — pin each service's OWN `:latest`->digest instead.

Source: leo-customer360 deployments/lib/ghcr.sh — enabling RESOLVE_DIGEST, 2026-08.

## Related

- [[Verify a deployed service isn't stale: match box RepoDigest to GHCR :latest and read its sha-<commit> tag]]
