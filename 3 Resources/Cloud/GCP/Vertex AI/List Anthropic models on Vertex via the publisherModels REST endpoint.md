---
tags: [vertex-ai, anthropic, gcp, rest-api, model-discovery]
---

# List Anthropic models on Vertex via the publisherModels REST endpoint

To enumerate the Claude models Vertex offers in a region, call the **publisher models** REST endpoint (there is no clean `gcloud` command for partner/publisher models — `gcloud ai model-garden models list` needs `publishers/*` access and a quota project, and defaults to the wrong consumer):

```
GET https://{REGION}-aiplatform.googleapis.com/v1beta1/publishers/anthropic/models
Authorization: Bearer <ADC token>
x-goog-user-project: <your-gcp-project>     # REQUIRED with user ADC — the quota project
```

- Token: `gcloud auth application-default print-access-token` (ADC — the same identity the agent uses), **not** `gcloud auth print-access-token` (that's the gcloud user account, which can differ).
- Response: `{ publisherModels: [{ name: "publishers/anthropic/models/claude-sonnet-4-5", versionId: "20250929", launchStage: "GA" }, ...], nextPageToken? }`.
- Build the Vertex model ID as **`<shortName>@<versionId>`** (e.g. `claude-sonnet-4-5@20250929`) — that's the form `ANTHROPIC_MODEL` / `--model` expect on Vertex.
- Paginate with `pageSize` + `pageToken`.

**Gotchas:**
- Without `x-goog-user-project`, user-ADC calls fail: *"the aiplatform.googleapis.com API requires a quota project"* and the consumer defaults to Google's shared project `32555940559` → `SERVICE_DISABLED`.
- The list is the region **catalog** — a *superset* of what the project can actually invoke until the Anthropic model terms are accepted in Model Garden. See [[Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint]].
- `global`/unset region has no `{region}-` host; list against a real region like `us-east5` (where the Claude models are offered).

Used in Vinnstack at `app/api/vertex/models/route.ts`. Related: [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]].
