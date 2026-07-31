---
title: "App-side read ECONNRESET to Postgres often means the Cloud SQL proxy sidecar is 403-unauthorized"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04, vinnstack sign-in failure"
tags: [gke, cloud-sql, iam, debugging, vinnstack]
---

# App-side read ECONNRESET to Postgres often means the Cloud SQL proxy sidecar is 403-unauthorized

Symptom: an app on GKE that reaches Postgres via a Cloud SQL Auth Proxy sidecar logs `read ECONNRESET` on DB queries (here: "accountCreds.api GET failed" / "processFlows.read DB read failed" right after Google sign-in). The app error is a SYMPTOM - the cause lives in the SIDECAR.

Diagnosis path that worked:
1. App error logs show ECONNRESET, no detail. Pivot to the sidecar container's logs.
2. The proxy runs with `--structured-logs`, so its logs are jsonPayload, not textPayload - grep for `jsonPayload|message:`, not the plain-text filter.
3. Proxy reveals the real error: `Error 403 NOT_AUTHORIZED: missing permission cloudsql.instances.get on instances/<db>`. The proxy accepts the local TCP ("Accepted connection from 127.0.0.1") then fails the upstream cert refresh - so connections reset rather than never open.
4. Identify the proxy's identity: `kubectl get secret <gsa-key> -o jsonpath='{.data.key\.json}' | base64 -d | grep client_email` (or the WI-bound KSA). Check `gcloud projects get-iam-policy PROJECT --flatten='bindings[].members' --filter='bindings.role=roles/cloudsql.client'`.
5. Fix: grant `roles/cloudsql.client` to that service account. Proxy retries metadata refresh on a loop - recovers within ~1-2 min, no pod restart.

Meta-lesson: a manifest comment that says "the GSA needs role X" is documentation, not enforcement - the binding can be absent. When wiring a new workload+proxy, verify the binding exists, don't trust the comment.

## Related

- [[Diagnose GCP console permission errors with the testIamPermissions REST probe]]
