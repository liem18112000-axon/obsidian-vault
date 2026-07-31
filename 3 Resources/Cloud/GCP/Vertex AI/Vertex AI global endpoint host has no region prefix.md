---
title: "Vertex AI global endpoint host has no region prefix"
created: 2026-07-11
type: lesson
status: seedling
source: "session 2026-07-11, klara-nonprod Claude on Vertex setup"
tags: [gcp, vertex-ai, gotcha, api]
---

# Vertex AI global endpoint host has no region prefix

The Vertex AI **global** endpoint uses the bare host `aiplatform.googleapis.com` with no region prefix — unlike every regional endpoint, which prefixes the host with the region name (e.g. `us-east5-aiplatform.googleapis.com`, `europe-west1-aiplatform.googleapis.com`).

Location still selects `global` in the URL **path**, not the host:

```
https://aiplatform.googleapis.com/v1/projects/{PROJECT}/locations/global/publishers/anthropic/models/{MODEL}:rawPredict
```

The tempting-but-wrong pattern is to mechanically apply the regional template and produce `global-aiplatform.googleapis.com` — that host doesn't exist. Google's frontend returns a generic HTML 404 page for it, which looks like a routing/typo error rather than a real API response (contrast with the JSON-formatted 404 the actual aiplatform API returns for a real-but-inaccessible resource — see [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]]). If a loop that iterates regions by string-templating the host silently includes "global", it will produce this misleading HTML 404 unless global is special-cased to drop the host prefix.

## Related

- [[Vertex AI 404 vs 403 distinguishes Model Garden enablement from IAM permission]]
- [[Claude Sonnet 5 confirmed working on Vertex AI for klara-nonprod]]
