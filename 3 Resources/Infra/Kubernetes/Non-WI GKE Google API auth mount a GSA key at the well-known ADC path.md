---
title: "Non-WI GKE Google API auth: mount a GSA key at the well-known ADC path"
created: 2026-07-03
type: howto
status: seedling
source: "Vinnstack GKE deploy 2026-07-03"
tags: [gke, gcp, adc, cloud-sql-proxy, vertex, auth]
---

# Non-WI GKE Google API auth: mount a GSA key at the well-known ADC path

When you cannot use Workload Identity but a GKE pod must call Google APIs (Cloud SQL Auth Proxy, Vertex AI ADC), mount a GSA JSON key as a Secret. On a WI-enabled cluster the node-SA fallback is off, so a mounted key is the only non-WI path.

**Wiring that satisfies every consumer:**
- Cloud SQL Auth Proxy: pass `--credentials-file=/var/secrets/gcp/key.json` (or set GOOGLE_APPLICATION_CREDENTIALS).
- App code that shells out to `gcloud auth application-default print-access-token`: honors GOOGLE_APPLICATION_CREDENTIALS, OR the well-known file `$HOME/.config/gcloud/application_default_credentials.json`.
- App code that checks for ADC by `existsSync($HOME/.config/gcloud/application_default_credentials.json)`: ONLY satisfied by the file at that exact well-known path — NOT by GOOGLE_APPLICATION_CREDENTIALS and NOT by metadata-server ADC. This is why Workload Identity would never clear such a check.

**Trick for the file-check:** an initContainer that copies the mounted key to `$HOME/.config/gcloud/application_default_credentials.json`. If HOME is on a persistent volume shared with the app container, the seeded file is visible to the app.

**Tradeoff:** a long-lived downloadable key is a security downgrade vs WI, and is often blocked by org policy (`constraints/iam.disableServiceAccountKeyCreation`). Store only in the cluster Secret, shred the local copy, rotate.

## Related

- [[Creating the GSA a KSA annotation references activates WI routing and can break a pod]]
