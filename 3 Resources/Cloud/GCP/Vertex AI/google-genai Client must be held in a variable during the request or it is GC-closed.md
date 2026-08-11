---
ai_hash: 29a2650623ed9bc9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: Accesstrade integration, session 2026-06-13
status: seedling
tags:
- vertex-ai
- google-genai
- python
- gotcha
- gc
title: google-genai Client must be held in a variable during the request or it is
  GC-closed
type: lesson
---

# google-genai Client must be held in a variable during the request or it is GC-closed

With the **google-genai** SDK (used for Vertex AI Gemini), you must keep the `Client` object in a local variable for the duration of a call. Creating it inline and chaining the request in one expression lets the `Client` be garbage-collected mid-request, raising **"Cannot send a request, as the client has been closed."**

**Bad** — the temporary `Client` has no remaining reference after the attribute access resolves, so GC can close its underlying transport before the request finishes:

```python
return self._client().models.generate_content(...)   # client GC-closed mid-call
```

**Good** — bind it first so a live reference is held across the call:

```python
client = self._client()
return client.models.generate_content(...)
```

This bit during the Accesstrade `VertexClient` work (`use_cases/shared/llm.py`). The symptom is intermittent/confusing because it depends on GC timing.

See also [[Mounting host gcloud ADC into a container to authenticate Vertex AI]].

## Related

- [[Mounting host gcloud ADC into a container to authenticate Vertex AI]]

%% ai-graph-start %%

**Related notes:**
- [[Vertex models.list() shows the catalog, not what the project can invoke; Gemini 3 needs the global endpoint]]
- [[Mounting host gcloud ADC into a container to authenticate Vertex AI]]

%% ai-graph-end %%