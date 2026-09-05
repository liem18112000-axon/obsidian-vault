---
title: "gcloud logging read --freshness is unreliable with broad OR filters; use explicit timestamp bound"
created: 2026-08-13
type: lesson
status: seedling
source: "dev zip-import test suite 2026-08-13"
tags: [gcloud, logging, gotcha, gke]
---

# gcloud logging read --freshness is unreliable with broad OR filters; use explicit timestamp bound

`gcloud logging read --freshness=40m` is **unreliable** when combined with a broad `textPayload:(A OR B OR ...)` filter and `--order=asc` — it returned entries weeks old and from the wrong tenant instead of the last 40 minutes.

## Reliable pattern
Compute an explicit RFC3339 lower bound in the shell and filter on it:

```bash
SINCE=$(date -u -d "35 minutes ago" "+%Y-%m-%dT%H:%M:%SZ")
gcloud logging read \
  "resource.type=\"k8s_container\" AND ... AND timestamp>=\"$SINCE\" AND textPayload:(...)" \
  --order=asc --limit=80
```

Scope the text filter by tenant id to cut noise. Use `--order=asc` only alongside the explicit `timestamp>=` bound.

## Related

- [[google-skill-gke-logs]]
