---
title: "Cloud Run v2 multi-container sidecar in Terraform"
created: 2026-08-31
type: howto
status: seedling
source: "session 2026-08-31 test-agent sidecar"
tags: [terraform, cloud-run, sidecar, multi-container, gcp, iac]
---

# Cloud Run v2 multi-container sidecar in Terraform

Terraform recipe for a Cloud Run v2 service with sidecars (`google_cloud_run_v2_service`, provider >= 6):

- Emit multiple `containers {}` blocks. **Exactly one** container sets a `ports { container_port = N }` block — that is the *ingress* container; it receives external traffic and the injected `PORT` env. The rest are sidecars, reachable only on `localhost`.
- Every container needs a **`name`**. Sidecars listen on a fixed port you choose (e.g. bind uvicorn to `--port 8081`); `PORT` is set only on the ingress container, so don't rely on it in sidecars (and don't set `PORT` env yourself — it's reserved).
- Container **startup order**: set container-level `depends_on = ["<other-container-name>"]` on the ingress so the sidecar starts first.
- **Probes target a port**: `startup_probe { http_get { path = "/livez" port = 8081 } }` — give sidecar probes the sidecar's port, not the ingress port.
- **All containers share ONE service account** (`template.service_account`). Co-locating an agent + its bridge means the SA needs the UNION of both's permissions; the separate per-component SAs collapse to one.
- Driving containers from a `dynamic "containers"` over `{ for c in var.containers : c.name => c }` keys them by name (stable identity), avoiding order-diff churn.
- Migrating existing single-container services in-place: keep the same service `name` and `terraform state mv` the resource to its new module address, so it's an UPDATE (adds the sidecar) not a destroy+recreate. Verify with `terraform plan`.

See [[Co-locating a stateful MCP bridge as an agent sidecar couples their scaling]] for the *why*, and [[Refactor Terraform resources into a module as a no-op via terraform state mv]] for the state-mv technique.

## Related

- [[Co-locating a stateful MCP bridge as an agent sidecar couples their scaling]]
- [[Refactor Terraform resources into a module as a no-op via terraform state mv]]
