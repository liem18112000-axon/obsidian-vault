---
title: "Testing-Agent GCS memory bank: one bucket, memory/ root, five subfolders"
created: 2026-08-29
type: concept
status: seedling
source: "session 2026-08-29"
tags: [testing-agent, gcs, memory-bank, ai-agentic-framework]
---

# Testing-Agent GCS memory bank: one bucket, memory/ root, five subfolders

The Testing-Agent (ai-agentic-framework/test-agent) persists all pipeline output to a **single GCS bucket** — `GCS_BUCKET = mt-receive-ai-agent-memory` (project `klara-nonprod`) — under one prefix root, `memory/`. The root is defined once as `ROOT = "memory"` in `knowledge_gathering/memory/bank.py`. Both agents (knowledge-gathering and test-plan-definition) share the same bucket; each stage writes its **own sub-prefix**, so two runs that share a `context_id` never clobber each other.

Under `memory/` the code writes **five** subfolders (a common mistake is to assume three):

- `index/` — knowledge-index graph (json + md, written with a compare-and-set generation guard) — KG bank
- `notes/` — `jira/<KEY>`, `confluence/<id>`, `insight/<id>` curated notes — KG bank
- `refine/<context_id>/` — Step 2: questions, answers, understanding.md, state — KG bank
- `test-plan/<context_id>/` — Step 3+4: plan, decisions, scenarios, steps, features — `test_plan_definition/memory/writers.py`
- `runs/` — run-logs (`<ts>_run/refine/plan-<id>.md`) — both agents

**Why it matters:** to reset the bank you must clear all five prefixes; wiping only the per-context folders leaves `index/` pointing at deleted notes. Sources: `knowledge_gathering/memory/bank.py`, `test_plan_definition/memory/writers.py`, `docs/USAGE.md`.

## Related
[[clear_memory.sh wipes the Testing-Agent GCS memory bank (preview-first)]]

## Related

- [[clear_memory.sh wipes the Testing-Agent GCS memory bank (preview-first)]]
