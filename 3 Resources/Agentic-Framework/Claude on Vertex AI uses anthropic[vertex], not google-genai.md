---
title: "Claude on Vertex AI uses anthropic[vertex], not google-genai"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — test-agent Distill"
tags: [vertex-ai, claude, anthropic, gcp, gotcha]
---

# Claude on Vertex AI uses anthropic[vertex], not google-genai

To call **Claude models on Vertex AI** from Python, use the Anthropic SDK with the Vertex extra — `pip install "anthropic[vertex]"`, then `from anthropic import AnthropicVertex` (client takes `project_id` + `region`). Do NOT use `google-genai` — that is Google’s SDK for **Gemini**, a different model family.

**Gotcha:** `claude-sonnet-5` on Vertex is served only in the **`global`** region for our project, so set `vertex_region = global` (a real GCP location like `europe-west1` returns model-not-found). IAM is the same as any Vertex call: the runtime service account needs `roles/aiplatform.user` (Vertex AI API), no extra role for Claude.

Decision on the Knowledge-Gathering agent (LUZ-159671): the Distill step uses `AnthropicVertex` with `claude-sonnet-5` in `global`; env `VERTEX_PROJECT/VERTEX_LOCATION/VERTEX_MODEL`.

## Related

- [[a2a-sdk serves the agent card at agent-card.json (new) or agent.json (old)]]
