---
title: "Vertex AI global endpoint hostname has no regional prefix"
created: 2026-07-11
type: lesson
status: evergreen
source: "virtual-avatar session, 2026-07-11 — user-confirmed live test against klara-nonprod"
tags: [gcp, vertex-ai, claude, rest-api, gotcha]
---

# Vertex AI global endpoint hostname has no regional prefix

When calling Claude on Vertex AI's **global** endpoint directly via REST (rawPredict), the hostname is the plain `aiplatform.googleapis.com` — there is no `global-` prefix on the host. Only actual regional endpoints prefix the hostname with the region (e.g. `us-east5-aiplatform.googleapis.com`); "global" is not a region name to prepend, it only appears in the URL path's `locations/global` segment.

Confirmed working URL shape (klara-nonprod project, 2026-07-11):
`https://aiplatform.googleapis.com/v1/projects/klara-nonprod/locations/global/publishers/anthropic/models/claude-sonnet-5:rawPredict`

The initial failed attempt had incorrectly prefixed the host as `global-aiplatform.googleapis.com`, which isn't a real endpoint. Once corrected, the request returned HTTP 200 with a real Claude Sonnet 5 response — confirming Claude Sonnet 5 is live and reachable on Vertex AI's global endpoint for this project, validating the [[Virtual avatar presenter project design plan]]'s choice to use Vertex AI (see [[Claude models are available on GCP Vertex AI Model Garden]]) rather than a Claude.ai subscription for the audience-facing LLM calls.

## Related
- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[Virtual avatar presenter project design plan]]

## Related

- [[Claude models are available on GCP Vertex AI Model Garden]]
- [[Virtual avatar presenter project design plan]]
