---
title: "Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint"
created: 2026-06-14
type: lesson
status: seedling
source: "Accesstrade integration, session 2026-06-14"
tags: [vertex-ai, google-genai, gemini-3, gotcha, model-availability]
---

# Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint

On Vertex AI, `client.models.list()` (google-genai SDK) enumerates the **publisher catalog**, not the set of models the current project+region can actually invoke. A model can appear in `list()` yet return **404 NOT_FOUND "Publisher Model ... not found"** when you call `generate_content`.

Two separate reasons a listed Gemini model 404s on invoke:
1. **Wrong endpoint/region.** Gemini 3.x models are served from the **`global`** location, not regional ones like `us-central1`. Set `location="global"` (env `VERTEX_LOCATION=global`) to reach them. Gen-2.5 models work regionally.
2. **Not allowlisted for the project.** Even on `global`, some models stay 404 (e.g. `gemini-3-pro-preview` was unavailable to project `klara-nonprod` while `gemini-3.1-pro-preview` worked).

**Implication:** never trust `list()` to decide availability — probe with a real one-shot `generate_content`/`generate_json` call per candidate model and catch the 404. Verified 2026-06 against klara-nonprod: 3.1-pro-preview, 3.5-flash, 3-flash-preview all OK on `global`; all gen-3 404 on `us-central1`.

Related: [[Mounting host gcloud ADC into a container to authenticate Vertex AI]], [[google-genai Client must be held in a variable during the request or it is GC-closed]].

## Related

- [[Mounting host gcloud ADC into a container to authenticate Vertex AI]]
- [[google-genai Client must be held in a variable during the request or it is GC-closed]]
