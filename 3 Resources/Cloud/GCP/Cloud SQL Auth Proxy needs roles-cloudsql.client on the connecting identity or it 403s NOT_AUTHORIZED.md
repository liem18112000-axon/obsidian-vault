---
ai_hash: 67791a52c09c4a84
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-06
entities: []
source: vinnstack connect-cloud-db.ps1 2026-07-06; vinnstack GKE sign-in failure 2026-07-04
status: seedling
tags:
- cloud-sql
- iam
- gcp
- auth-proxy
- gke
- debugging
- vinnstack
title: Cloud SQL Auth Proxy needs roles/cloudsql.client on the connecting identity
  or it 403s NOT_AUTHORIZED
type: gotcha
---

# Cloud SQL Auth Proxy needs roles/cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED

The identity the Cloud SQL Auth Proxy authenticates as (your gcloud ADC locally, or the GSA / Workload-Identity-bound KSA on a pod) needs **`roles/cloudsql.client`** on the instance's **project** — it grants `cloudsql.instances.connect` + `cloudsql.instances.get`. Without it the proxy still starts and still accepts the local TCP connection ("Accepted connection from 127.0.0.1"), then fails the upstream cert refresh with `Error 403 NOT_AUTHORIZED: missing permission cloudsql.instances.get on instances/<db>`. This is purely IAM to reach the instance — the Postgres user/password is separate and unrelated.

**The app-side symptom is misleading:** connections reset rather than never open, so the application logs only `read ECONNRESET` on DB queries with no detail. The cause lives in the **sidecar**, not the app. Diagnosis path:
1. Pivot from app logs to the proxy container's logs.
2. The proxy runs with `--structured-logs`, so grep `jsonPayload`/`message:` — a plain-text filter finds nothing.
3. Identify the proxy's identity: `kubectl get secret <gsa-key> -o jsonpath='{.data.key\.json}' | base64 -d | grep client_email`.
4. Check and grant:
   ```bash
   gcloud projects get-iam-policy PROJECT --flatten="bindings[].members" \
     --filter="bindings.members:EMAIL" --format="value(bindings.role)"
   gcloud projects add-iam-policy-binding PROJECT --member="user:EMAIL" --role="roles/cloudsql.client"
   ```
   Use `serviceAccount:` when the email ends in `.gserviceaccount.com`. The proxy retries its metadata refresh on a loop and recovers within ~1–2 min — no pod restart needed.

Gotchas: the check itself needs `resourcemanager.projects.getIamPolicy`, so a failed check is *inconclusive*, not "missing"; the role can be inherited via a group, so a direct-binding check false-negatives — keep such checks a WARNING, never a hard block; and granting IAM usually needs a human admin, an agent can't self-grant. Meta-lesson: a manifest comment saying "the GSA needs role X" is documentation, not enforcement — verify the binding exists.

## Related

- [[3 Resources/Cloud/GCP/GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get]]
- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]

%% ai-graph-start %%

**Related notes:**
- [[GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get]]
- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]
- [[Deploying a stateful single-tenant app to GKE with a Cloud SQL proxy sidecar]]
- [[Creating the GSA a KSA annotation references activates WI routing and can break a pod]]
- [[Non-WI GKE Google API auth mount a GSA key at the well-known ADC path]]

%% ai-graph-end %%