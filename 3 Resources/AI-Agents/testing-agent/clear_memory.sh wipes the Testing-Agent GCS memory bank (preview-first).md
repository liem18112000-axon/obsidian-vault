---
title: "clear_memory.sh wipes the Testing-Agent GCS memory bank (preview-first)"
created: 2026-08-29
type: howto
status: seedling
source: "session 2026-08-29"
tags: [testing-agent, gcs, tooling, bash, ai-agentic-framework]
---

# clear_memory.sh wipes the Testing-Agent GCS memory bank (preview-first)

`test-agent/tools/clear_memory.sh` truncates (deletes every object in) the Testing-Agent GCS memory bank so the next pipeline run starts clean.

**Design decisions:**
- **Preview-first / dry-run by default.** A plain run only lists per-folder object counts and deletes nothing; you must re-run with `CONFIRM=1` to actually delete. This mirrors the `earchive-data-clean` skill convention — destructive ops never fire on the first invocation.
- **Config derives from the app .env.** It reads `GCS_BUCKET` / `GCP_PROJECT` from `../.env` (test-agent/.env — the same source `knowledge_gathering/config.py` uses) unless already exported, so the tool and the code can never disagree on which bucket is the bank. Only those two keys are grepped out — no secrets are sourced.
- **Per-folder loop over the five prefixes** (`index notes refine test-plan runs`). `FOLDERS="refine test-plan"` limits the wipe to a subset; `GCS_BUCKET=other` overrides the bucket.

**Mechanics:** counts objects with `gcloud storage ls "<prefix>**"` (the `**` glob matches leaf objects recursively; filter out trailing-slash placeholder lines), deletes with `gcloud storage rm --recursive <prefix>`. Requires gcloud authenticated via ADC.

**Gotcha:** to fully reset you want all five folders — see the linked layout note for why clearing only per-context folders leaves a dangling `index/`.

## Related
[[Testing-Agent GCS memory bank: one bucket, memory/ root, five subfolders]]

## Related

- [[Testing-Agent GCS memory bank: one bucket]]
- [[memory/ root]]
- [[five subfolders]]
