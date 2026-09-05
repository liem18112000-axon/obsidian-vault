---
title: "Adding a Cloud Run service that shares one image var: build first, targeted apply"
created: 2026-08-28
type: lesson
status: seedling
source: "session 2026-08-28, deploy test_plan_definition"
tags: [test-agent, terraform, cloud-run, deploy, gotcha]
---

# Adding a Cloud Run service that shares one image var: build first, targeted apply

Deploying the test_plan_definition agent + bridge onto the existing knowledge-gathering Terraform stack hit two traps worth remembering:

1. **One `image` var drives ALL Cloud Run services.** The new tpd services need a freshly built image (containing the new package); the live kga/bridge must stay on their current image. You cannot satisfy both in one full `terraform apply`. Fix: build+push the new image to the tag first, then `terraform apply -target=<each tpd resource>` so kga/bridge are excluded and keep their pinned digest. `terraform plan` first — confirm "0 to destroy" and that the only in-place changes are ones you intend.

2. **Pre-existing image drift.** `terraform plan` wanted to update kga+bridge in-place because their DEPLOYED image (`klara-nonprod/kga/kga@sha256:…`, a pinned digest) differed from tfvars `image` (`klara-repo/.../test-agent:latest`). A naive apply would have flipped the live services to a different registry/image — an unintended redeploy. The targeted apply avoided touching them; the drift remains and should be converged deliberately later (deploy.sh's "preserve deployed image from state" logic is what normally prevents this).

3. **Secret-version ordering.** A Cloud Run service mounting `secret:latest` fails to start if the secret has no version yet. Since the service + its secret are created in the same config, do a targeted apply of JUST the secret container first, `gcloud secrets versions add` a value, THEN apply the service.

Net sequence: build image -> targeted-apply the secret -> add secret version -> targeted-apply the rest of the new resources -> smoke-test (agent /livez 200, card lists skills, bridge /mcp bare returns 401). Result here: 7 added, 0 changed, 0 destroyed — kga untouched.

## Related

- [[Escape shell ${VAR} as $${VAR} in a Terraform Cloud Run command list]]
