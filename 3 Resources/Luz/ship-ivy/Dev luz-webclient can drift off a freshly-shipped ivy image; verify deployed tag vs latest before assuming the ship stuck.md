---
title: "Dev luz-webclient can drift off a freshly-shipped ivy image; verify deployed tag vs latest before assuming the ship stuck"
created: 2026-08-04
type: lesson
status: seedling
source: "session 2026-08-04 — rollout-only of luz-webclient to dev"
tags: [luz, ship-ivy, gke, rollout, kubernetes, gotcha, drift]
---

# Dev luz-webclient can drift off a freshly-shipped ivy image; verify deployed tag vs latest before assuming the ship stuck

After an ivy ship to dev, the `luz-webclient` Deployment image can silently **drift back** to an older tag within minutes of a successful rollout. Observed 2026-08-04: a ship rolled dev/luz-webclient to `dde6de138…` (the image carrying the target `luz_finance` commit) and an 8x poll confirmed it stable at 15:27; by ~15:31 the Deployment spec had reverted to the older `e90cd2318…` with no action from me.

**Two-image nature of an ivy webclient build** (why the tags matter):
- `e90cd2318…` = the webclient feature branch **before** the service CI bumps `module_hash.txt` → carries the OLD luz_finance module.
- `dde6de138…` = the SAME branch **after** the `module_hash.txt` bump to the target luz_finance commit → the image you actually want, and (right after the build) the **latest** tag.

**Lesson / checklist before assuming a ship "stuck":** dont trust the rollout success line alone — verify the *currently deployed* image against the *latest* registry tag:
```bash
kubectl get deployment luz-webclient -n dev \
  -o jsonpath="{.spec.template.spec.containers[?(@.name==\"luz-webclient\")].image}"
gcloud artifacts docker images list \
  europe-west6-docker.pkg.dev/klara-repo/artifact-registry-container-images/luz-webclient \
  --include-tags --sort-by=~UPDATE_TIME --limit=3 --format="table(UPDATE_TIME, TAGS)"
```
If deployed != the post-bump tag, re-roll: `NAMESPACE=dev bash ~/.claude/skills/google-skill-rollout-latest/rollout_latest.sh luz-webclient` (safe because the post-bump image is the latest tag).

**Caveat:** if the drift recurs after a re-roll, a plain rollout wont hold — it means an active reconciler (GitOps/ArgoCD) or another person is re-pinning the older image. Chase the *source* of the re-pin rather than re-rolling in a loop.

## Related

- [[ship-ivy stage 1 fails on Bitbucket read-replica lag when webclient branch already exists]]
- [[luz-skill-ship-ivy]]
