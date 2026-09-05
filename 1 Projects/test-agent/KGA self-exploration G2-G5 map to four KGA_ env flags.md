---
title: "KGA self-exploration G2-G5 map to four KGA_ env flags"
created: 2026-09-03
type: term
status: seedling
source: "session 2026-09-03"
tags: [kga, test-agent, knowledge-gathering, feature-flags]
---

# KGA self-exploration G2-G5 map to four KGA_ env flags

The KGA (knowledge-gathering-agent) "self-exploration" stack is four independently gated phases, each a default-OFF env flag (truthy = `1/true/yes/on`), all read in `knowledge_gathering` (the KGA service only, not TPD):

- **G2** `KGA_LLM_HYPOTHESIZE` — LLM-focused search terms (round-0 only).
- **G3** `KGA_FOLLOW_WEB` — fetch ticket-linked external-web URLs (else recorded-not-fetched).
- **G4** `KGA_LLM_LEADS` — external-LLM leads + grounding gate.
- **G5** `KGA_EXPLORE_LOOP` — the bounded, resumable multi-round explore loop that turns the single pre-crawl fan-out into fan-out -> crawl -> reflect -> next-focus -> repeat.

Key design point: the **G5 core loop needs NO LLM** — round-N focus is heuristic salient tokens over round N-1 node titles; G2/G4 are optional LLM add-ons layered on top and run round-0 only, thread-offloaded. G5 bounds (env-tunable): `KGA_EXPLORE_MAX_ROUNDS`=3, `KGA_EXPLORE_TIME_BUDGET`=300s total, `KGA_EXPLORE_ROUND_NODES`=20, `KGA_EXPLORE_ROUND_SECONDS`=120s — all kept under Cloud Runs 600s timeout, and loop state is persisted to GCS by `context_id` so a redeploy resumes mid-loop.

## Related
[[Terraform-managed Cloud Run: set env flags in TF, not gcloud run update]]

## Related

- [[Terraform-managed Cloud Run: set env flags in TF]]
- [[not gcloud run update]]
