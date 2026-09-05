---
title: "Claude on Vertex AI availability is per-project per-region (klara-nonprod)"
created: 2026-08-27
type: observation
status: seedling
source: "session 2026-08-27 models/test_vertex_claude.py"
tags: [vertex-ai, anthropic, claude, model-access, klara-nonprod]
---

# Claude on Vertex AI availability is per-project per-region (klara-nonprod)

Which Claude models a GCP project can call on Vertex AI is **granted per project AND per region** — the Model Garden catalog listing (\`GET {region}-aiplatform.googleapis.com/v1beta1/publishers/anthropic/models\`) shows what *exists*, not what *your project can invoke*. The only reliable test is to actually send a request (rawPredict) per model.

**Probe of project \`klara-nonprod\` on 2026-08-27** (via \`models/test_vertex_claude.py\`): the ONLY usable model was **\`claude-sonnet-5\`** (region \`global\` returned a real reply). Every Opus/Haiku/older Sonnet ID returned 404 in \`global\`, \`us-east5\`, and \`europe-west1\`.

**Reading the error codes — they mean different things:**
- **404** "was not found or your project does not have access to it" = no access for that model in that region.
- **403** "Access to this model requires data sharing ... set PublisherModelConfig.data_sharing_enabled_provider to anthropic" = model exists and could be enabled, but is gated behind a data-sharing toggle (seen on \`claude-fable-5\`).
- **429** resource_exhausted/quota = **access IS granted**, but no quota/throughput provisioned right now (\`claude-sonnet-5\` gave 404 nowhere but 429 in us-east5 & europe-west1, 200 OK in global). So 429 confirms reachability, not absence.

**Practical takeaways:** use \`region="global"\` — it carries the full modern lineup and worked for sonnet-5; a 404 is an access problem (request access / enable the model in Model Garden), a 403 needs the data-sharing setting, a 429 means try \`global\` or provision quota. \`claude-fable-5\` also needs 30-day data retention besides the data-sharing toggle.

Related: [[AnthropicVertex auth uses ADC not the active gcloud account]]

## Related

- [[AnthropicVertex auth uses ADC not the active gcloud account]]

## Enabling a blocked model (opus/fable) for a project

A 404/403 is fixed by **enabling the model in Model Garden for that project** (accept the Anthropic terms; for Fable also toggle data sharing to Anthropic). This is a one-time admin action, NOT something an access token or the SDK can flip at call time:

- `gcloud ai model-garden models` only exposes `deploy` / `list` / `list-deployment-config` - there is **no** `accept-eula` / `enable` subcommand for Claude MaaS models.
- The publisher-model-config REST route (`.../publishers/anthropic/models/{m}?view=...`) 404s at the gateway for `global`.
- Evidence it is per-model: in `klara-nonprod`, `claude-sonnet-5` was enabled (worked) while `claude-opus-*` returned 404 in the same project+region - someone enabled sonnet-5 specifically.

**Do it in the Console:** Vertex AI -> Model Garden -> the Anthropic model -> **Enable** (per model). Needs `roles/aiplatform.user` plus permission to accept Model Garden terms (typically `roles/aiplatform.admin` / console access). After enabling, call it exactly like sonnet-5 with `region="global"`.
