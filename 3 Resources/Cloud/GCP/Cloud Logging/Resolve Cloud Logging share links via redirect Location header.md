---
title: "Resolve Cloud Logging share links via redirect Location header"
created: 2026-06-11
type: howto
status: seedling
source: "sessions 2026-06-11 and 2026-06-12 (luz-docs behave run from a teammate link)"
tags: [gcp, cloud-logging, gcloud, kubernetes, webfetch, gke]
---

# Resolve Cloud Logging share links via redirect Location header

A `https://cloudlogging.app.goo.gl/<id>` share link is only a shortener — it 302s to the full Logs Explorer URL, so you never need the console UI. Get the target with `curl -sI <link> | grep -i location` (or **WebFetch**, which returns the same `Location`: `https://console.cloud.google.com/logs/query;...`).

The redirect path carries everything needed to reproduce the query headlessly:
- `query=` — the URL-encoded Cloud Logging filter (decode `%3D`→`=`, `%22`→`"`, `%0A`→newline/AND).
- `startTime=` / `endTime=` — the time window.
- `cursorTimestamp=` — where the viewer was scrolled.

Then run it yourself:

```bash
gcloud logging read '<filter> AND timestamp>="<startTime>" AND timestamp<="<endTime>"' \
  --project=<project> --order=asc --format='value(textPayload)'
```

The decoded filter typically pins `resource.labels.project_id`, `cluster_name`, `namespace_name`. For Kubernetes **Job** pods the stable selector is `labels."k8s-pod/batch_kubernetes_io/controller-uid"="<uid>"` — it matches every pod of one Job run regardless of pod name.

Quoting the resulting filter from PowerShell 5.1 needs care: see [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]].

## Related

- [[Cloud Logging share link endTime can truncate a job's logs mid-run]]
- [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]]
