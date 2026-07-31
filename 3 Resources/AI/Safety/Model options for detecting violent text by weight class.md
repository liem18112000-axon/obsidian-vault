---
ai_hash: 77318239bfbfb6d8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities: []
source: web research session 2026-07-20
status: seedling
tags:
- content-moderation
- huggingface
- llm-safety
- models
title: Model options for detecting violent text by weight class
type: howto
---

# Model options for detecting violent text by weight class

Options for classifying violent text, lightest to heaviest:

- **KoalaAI/Text-Moderation** — DeBERTa-v3 encoder, ~180MB, runs locally on CPU, OpenAI-style labels (V=violence, V2=graphic violence, HR=harassment/threat). English-only. Good minimal default.
- **Detoxify** — toxicity classifier with a `threat` label; narrower than a violence taxonomy.
- **Llama-Guard-3-1B / -8B** — LLM-based safeguard, MLCommons 13-hazard taxonomy, classifies both prompts and responses; heavier but more nuanced and policy-promptable.
- **Hosted**: OpenAI moderation endpoint (free, per-category scores), Google Perspective API (THREAT attribute), Azure Content Safety (severity levels).

Whichever is chosen, the threshold must be calibrated on labeled examples from the target domain — defaults transfer badly. Example implementation: C:\Users\dvtliem\AI\ai-test\violence_detector.py

## Related

- [[3 Resources/AI/Safety/Violence detection needs a trained classifier, not keyword lists]]
- [[Moderation taxonomies split violence into subtypes]]

%% ai-graph-start %%

**Related notes:**
- [[Moderation taxonomies split violence into subtypes]]
- [[Violence detection needs a trained classifier, not keyword lists]]
- [[Property damage falls outside person-directed violence taxonomies]]

%% ai-graph-end %%