---
tags: [claude-code, vertex-ai, gcp, llm-backend]
---

# Claude Code runs on Vertex AI via three env vars with gcloud ADC

The `claude` CLI (Claude Code) can be pointed at **Claude-on-Google-Vertex-AI** instead of the direct Anthropic API/subscription by setting three environment variables on the spawned process:

- `CLAUDE_CODE_USE_VERTEX=1`
- `ANTHROPIC_VERTEX_PROJECT_ID=<gcp-project>`
- `CLOUD_ML_REGION=<region>` (e.g. `us-east5`, `europe-west1`, or `global`)

Auth is **gcloud application-default credentials (ADC)** — *not* an API key. So when routing to Vertex you should also strip `ANTHROPIC_API_KEY` / `ANTHROPIC_AUTH_TOKEN` / `ANTHROPIC_BASE_URL` so a stray direct-API key can't hijack billing.

**Gotchas:**
- It's still the **same Claude models** — Vertex is a routing/billing swap, not a different assistant. Every agentic capability is unchanged.
- **Model IDs differ**: `--model` / `ANTHROPIC_MODEL` must be a Vertex-supported ID (Vertex uses `claude-opus-4-8@YYYYMMDD`-style names), and the model must exist in the chosen `CLOUD_ML_REGION`.
- The model list on Vertex **lags** Anthropic's direct API, and some API features (prompt caching, batch, server-side tools) may be unavailable or gated.
- Because a system-wide `CLAUDE_CODE_USE_VERTEX` env var will silently force Vertex, when you want the direct login you must actively **delete** those three vars from the child env.

Related: [[Mounting host gcloud ADC into a container to authenticate Vertex AI]], [[Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint]].
