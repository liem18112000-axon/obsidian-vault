---
title: "leo-customer360 CD: UAT deploys only from main + --deploy-uat marker"
created: 2026-08-21
type: observation
status: seedling
source: "session 2026-08-21"
tags: [leo-customer360, cicd, github-actions, deployment, gotcha]
---

# leo-customer360 CD: UAT deploys only from main + --deploy-uat marker

How the **leo-customer360** GitHub Actions CI/CD gates deployments (dug out of .github/workflows/ci.yml + cd.yml):

## The gating rules
- **CI (ci.yml)** runs tests on every branch (`branches: ['**']`), but only **builds + pushes images to GHCR** when `github.ref == refs/heads/main` OR a `v*` tag. On a feature branch: tests only, **no new images**.
- **CD (cd.yml)** triggers on `workflow_run` after CI succeeds, and deploys **uat only when the ref is `main` AND the head commit title contains the literal marker `--deploy-uat`** (opt-in). It also supports manual `workflow_dispatch` (choose env/tag/services) and `vX.Y.Z` tags. It then runs `bash deploy-all.sh <env> --only <services> -y`, pulling `:latest`.

## Consequences (the gotcha)
- Committing `--deploy-uat` on a **feature branch does nothing** — no images built, no deploy. The marked commit must be on **main**.
- Because CD reads the marker from **main's head commit title**, a plain `Merge pull request #N` merge commit will **NOT** carry it -> deploy skipped. To trigger via PR, **squash-merge and keep `--deploy-uat` in the squash title** (set the PR title to include it so the default squash title carries it), or push the marked commit directly to main.
- Team convention: deploy commits look like `chore: redeploy all vServer services (run N) --deploy-uat`.

## Service SCOPE gotcha (bit me on the Jaeger SSO deploy)
On a `--deploy-uat` push the CD job hardcodes **`services=api,backend,ads,frontend`** (the `DEFAULT_SVC` in cd.yml) and runs `deploy-all.sh uat --only "$SERVICES"`. So a `--deploy-uat` merge NEVER deploys the **`monitoring`** or **`load-balancer`** modules — changes to those (e.g. a new Jaeger container, oauth2 gate, or an LB backend) land on `main` and CD goes green, but are **not applied to the box**. Deploy them manually: `(cd deployments/monitoring && ./deploy-monitoring.sh uat)` and `(cd deployments/load_balancer && ./deploy.sh uat apply)`; or use the manual `workflow_dispatch` on cd.yml with an explicit `services=` input. (Only api/backend/ads/frontend also get fresh GHCR images; the other modules are config/terraform, not images.)

## Related
[[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]

## Service SCOPE gotcha (bit me on the Jaeger SSO deploy)
On a `--deploy-uat` push the CD job hardcodes **`services=api,backend,ads,frontend`** (the `DEFAULT_SVC` in cd.yml) and runs `deploy-all.sh uat --only "$SERVICES"`. So a `--deploy-uat` merge NEVER deploys the **`monitoring`** or **`load-balancer`** modules — changes to those (e.g. a new Jaeger container, oauth2 gate, or an LB backend) land on `main` and CD goes green, but are **not applied to the box**. Deploy them manually: `(cd deployments/monitoring && ./deploy-monitoring.sh uat)` and `(cd deployments/load_balancer && ./deploy.sh uat apply)`; or use the manual `workflow_dispatch` on cd.yml with an explicit `services=` input. (Only api/backend/ads/frontend also get fresh GHCR images; the other modules are config/terraform, not images.)

## Related

- [[leo-customer360 deploys as Docker containers on VNG vServer VMs over SSH]]
