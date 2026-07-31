---
title: "tiktoken downloads its BPE vocab on first use"
created: 2026-06-17
type: lesson
status: seedling
source: "session 2026-06-17"
tags: [python, tiktoken, offline, llm, gotcha]
---

# tiktoken downloads its BPE vocab on first use

`tiktoken` does **not** ship its BPE vocabularies; it **downloads them from a public blob store on first use** (e.g. `tiktoken.encoding_for_model('gpt-3.5-turbo')` fetches `cl100k_base`). On an **offline / air-gapped** machine this throws.

**Fix:** set the env var `TIKTOKEN_CACHE_DIR` to a directory, **pre-warm** the cache on an online machine (call `get_encoding`/`encoding_for_model` once so the files land there), then ship that directory and point `TIKTOKEN_CACHE_DIR` at it at runtime. This is one of several 'fetches-on-first-use' traps for offline AI bundles (HuggingFace model downloads are another).

## Related

- [[Python embeddable distribution needs import site enabled for pip]]
