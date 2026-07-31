---
title: "Expand a cloudlogging.app.goo.gl share link via WebFetch to recover the gcloud filter"
created: 2026-06-12
type: howto
status: seedling
source: "session 2026-06-12"
tags: [gcloud, cloud-logging, webfetch, gke]
---

# Expand a cloudlogging.app.goo.gl share link via WebFetch to recover the gcloud filter

A Cloud Logging "share" link (`https://cloudlogging.app.goo.gl/<id>`) is just a shortener for the full Logs Explorer URL. Calling **WebFetch** on it returns a 302 whose `Location` is `https://console.cloud.google.com/logs/query;...`, carrying everything you need to reproduce the query headlessly:

- `query=` — the URL-encoded Cloud Logging filter (decode `%3D`→`=`, `%22`→`"`, `%0A`→newline/AND).
- `startTime=` / `endTime=` — the time window.
- `cursorTimestamp=` — where the viewer was scrolled.

So you never need the browser/console UI: expand the link, URL-decode `query`, and feed it to `gcloud logging read` with `timestamp>=` / `timestamp<=` bounds from start/end. The decoded filter typically pins `resource.labels.project_id`, `cluster_name`, `namespace_name`, and a `labels."k8s-pod/batch_kubernetes_io/controller-uid"` for a specific Job pod.

Tested 2026-06-12 to pull a luz-docs integration-test (behave) run from a teammate-shared link.

See [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]] for the quoting gotcha when running the resulting `gcloud logging read`.

## Related

- [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]]
