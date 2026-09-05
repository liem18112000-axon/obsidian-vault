---
title: "Istio classic sidecar keeps a batch Job pod from Completing unless the app hits /quitquitquit"
created: 2026-08-18
type: lesson
status: seedling
source: "code review LUZ-158230 k6 load-test, 2026-08-18"
tags: [kubernetes, istio, jobs, gotcha]
---

# Istio classic sidecar keeps a batch Job pod from Completing unless the app hits /quitquitquit

A Kubernetes **batch Job** with an injected **classic** (non-native) Istio sidecar can never reach `Completed`. When the main container exits, the `istio-proxy` (Envoy) container keeps running, so the pod stays `Running`/`NotReady` forever. Consequences: `ttlSecondsAfterFinished` never fires (it only counts from pod *completion*), pods pile up run-over-run, and any wait-for-completion tooling hangs.

Fixes (pick one):
- App signals Envoy to quit at the end of the wrapper: `curl -sf -XPOST http://localhost:15020/quitquitquit || true` before `exit`.
- Disable injection for the Job: `sidecar.istio.io/inject: "false"` annotation on the pod template.
- Use **native** sidecars (Istio `values.pilot.env.ENABLE_NATIVE_SIDECARS=true`, K8s 1.28+), where the sidecar runs as an init-container with `restartPolicy: Always` and Kubernetes tears it down when the main container exits — no `/quitquitquit` needed.

So: whether this bites depends on whether the mesh uses classic or native sidecars. Seen in LUZ-158230 k6 load-test (`k6/gke/k8s.yaml`); the report-upload itself was unaffected, only pod cleanup.

## Related

- [[Istio sidecar injection]]
- [[Kubernetes Job]]
