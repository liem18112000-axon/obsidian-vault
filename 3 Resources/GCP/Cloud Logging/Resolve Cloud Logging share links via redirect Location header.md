---
title: "Resolve Cloud Logging share links via redirect Location header"
created: 2026-06-11
type: howto
status: seedling
source: "session 2026-06-11"
tags: [gcp, cloud-logging, gcloud, kubernetes]
---

# Resolve Cloud Logging share links via redirect Location header

A `cloudlogging.app.goo.gl/...` share link is just a redirect. `curl -sI <link> | grep -i location` returns the full Cloud Console URL whose path embeds the URL-encoded Cloud Logging query plus `startTime`/`endTime`. Decode that query and feed it to `gcloud logging read '<filter>' --project=... --order=asc --format='value(textPayload)'` to dump the same logs headlessly.

For Kubernetes Job pods, the stable selector in the filter is the label `labels."k8s-pod/batch_kubernetes_io/controller-uid"="<uid>"` — it identifies all pods of one Job run regardless of pod name.

See also [[Cloud Logging share link endTime can truncate a job's logs mid-run]].

## Related

- [[Cloud Logging share link endTime can truncate a job's logs mid-run]]
