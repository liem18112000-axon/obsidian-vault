---
title: "Rollout restart uses the LIVE spec - a manifest edited only in git changes nothing"
created: 2026-07-04
type: gotcha
status: seedling
source: "session 2026-07-04, vinnstack auth env rollout"
tags: [kubernetes, kubectl, gitops, env-vars]
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
