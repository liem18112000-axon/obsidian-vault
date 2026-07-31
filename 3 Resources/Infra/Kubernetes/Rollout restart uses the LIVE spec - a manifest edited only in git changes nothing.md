---
ai_hash: 392c82ee46354a87
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack auth env rollout
status: seedling
tags:
- kubernetes
- kubectl
- gitops
- env-vars
title: Rollout restart uses the LIVE spec - a manifest edited only in git changes
  nothing
type: gotcha
---

# Rollout restart uses the LIVE spec - a manifest edited only in git changes nothing

Adding env vars (or any pod-spec change) to the YAML in the repo and then running `kubectl rollout restart` does NOTHING for that change: restart re-creates pods from the CLUSTER's stored spec, and the git file was never applied. The new secretKeyRef silently isn't there, and with `optional: true` refs there is no error to notice - the feature just stays dark.

When a full `kubectl apply -f` is unsafe because CI owns part of the spec (e.g. cloudbuild pins the image with `kubectl set image`, while the repo manifest says `:latest` - applying would trigger an unintended image rollout), patch ONLY the fields you changed:

    kubectl -n NS patch statefulset NAME --patch-file env-patch.json

with a strategic-merge patch addressing the container BY NAME; `env` entries merge by name, so existing vars (DATABASE_URL) survive. The patch itself triggers the rollout - no separate restart needed. Then reconcile the repo manifest so git matches live.

Symptom to recognize: "I added the env var and restarted, but the app behaves as if it's unset."

## Related

- [[GKE managed-cert HTTPS: global IP]]
- [[DNS before cert]]
- [[NEG service]]
- [[FrontendConfig redirect]]

%% ai-graph-start %%

**Related notes:**
- [[GKE managed-cert HTTPS global IP, DNS before cert, NEG service, FrontendConfig redirect]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]
- [[kubectl rollout status timeout must cover cold image pull time]]
- [[Split Terraform cluster-provisioning state separate from in-cluster workload state]]
- [[Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar]]

%% ai-graph-end %%