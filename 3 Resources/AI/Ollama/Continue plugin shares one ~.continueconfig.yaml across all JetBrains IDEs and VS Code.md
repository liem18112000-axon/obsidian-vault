---
title: "Continue plugin shares one ~/.continue/config.yaml across all JetBrains IDEs and VS Code"
created: 2026-07-01
type: howto
status: seedling
source: "session 2026-07-01"
tags: [continue, ollama, jetbrains, intellij, pycharm, ide]
---

# Continue plugin shares one ~/.continue/config.yaml across all JetBrains IDEs and VS Code

The Continue plugin reads a single global config at **`~/.continue/config.yaml`** (Windows: `C:\Users\<user>\.continue\config.yaml`) that is shared by **every** IDE it's installed in — IntelliJ IDEA, PyCharm, and VS Code all use the same file. So to wire local Ollama models into multiple JetBrains IDEs you configure the file **once**; the only per-IDE step is installing the Continue plugin from each IDE's marketplace (JetBrains has no reliable CLI to install plugins).

Config shape (schema v1): a top-level `models:` list where each entry has `provider: ollama`, `model: <ollama tag>`, and a **`roles:`** list. Roles map a model to a job: `chat`, `edit`, `apply`, `autocomplete`, `embed`. Good local split: a 7B coder for chat/edit/apply, a small **base** model (e.g. `qwen2.5-coder:1.5b-base`) for `autocomplete` (FIM needs to be fast), and `nomic-embed-text` for `embed` (powers @codebase retrieval).

See [[Best Ollama models for CPU-only coding and research on a thin laptop]].

## Related

- [[Best Ollama models for CPU-only coding and research on a thin laptop]]
