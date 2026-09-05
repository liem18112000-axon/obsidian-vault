---
title: "postgres skill dev port-forward needs the klara-nonprod GKE context, not kind-customer360"
created: 2026-08-04
type: lesson
status: seedling
source: "session 2026-08-04"
tags: [kubectl, context, luz-alloydb, postgres-skill, dev, gotcha]
---

# postgres skill dev port-forward needs the klara-nonprod GKE context, not kind-customer360

The `postgres` skill (`pg.ps1`, used by `luz-store-invoice-run-v2`) opens its dev connection via `kubectl port-forward service/luz-alloydb-main 5432:5432 -n dev`. That port-forward runs against **whatever the current kubectl context is** — so if the context has no `dev` namespace it fails with:

```
Error from server (NotFound): namespaces "dev" not found
port-forward did not come up on 127.0.0.1:5432 within ~16s.
```

The local default context `kind-customer360` (and `rancher-desktop`) have no `dev` namespace. The dev cluster is the GKE one:

```powershell
kubectl config use-context gke_klara-nonprod_europe-west6-a_klara-nonprod
```

Run that first, then the skill queries connect. This matches the org default (klara-nonprod / dev / europe-west6). If a query suddenly cannot reach Postgres on dev, check `kubectl config current-context` before assuming the DB or the skill is broken.

## Related

- [[luz-store-invoice-run-v2]]
