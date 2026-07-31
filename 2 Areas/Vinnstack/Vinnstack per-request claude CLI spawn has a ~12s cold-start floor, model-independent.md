---
title: "Vinnstack per-request claude CLI spawn has a ~12s cold-start floor, model-independent"
created: 2026-07-18
type: observation
status: seedling
source: "Vinnstack session 2026-07-18"
tags: [vinnstack, performance, claude-cli, latency, gotcha]
---

# Vinnstack per-request claude CLI spawn has a ~12s cold-start floor, model-independent

Measured on Vinnstack (Windows, 2026-07): a per-request headless `claude -p` spawn has a **~12–13 second cold-start floor**, and it is MODEL-INDEPENDENT — Haiku 4.5 measured 13.6s and Sonnet 4.6 measured 12.6s for the same tiny "translate one sentence" prompt (first-byte ~12s; the model generation itself is sub-second). Confirmed both via the app route (/api/assist: 14–17s total) and a raw `claude -p ... --output-format json` from the shell.

Implication: ANY Vinnstack feature that spawns the CLI per user action inherits this ~12s latency. Choosing a faster model (Haiku vs Sonnet) does NOT help — the cost is CLI process launch + init (node bundle load, auth check, config/CLAUDE.md load; likely worsened by Defender scanning the unsigned node bundle each spawn), not inference. Streaming vs buffered output format also does not change it.

The ONLY way to make a per-action AI call feel instant is to BYPASS the CLI with a direct HTTP call to api.anthropic.com. That needs either an ANTHROPIC_API_KEY (the app strips these by design — subscription-OAuth only, see agentEnv in ultracodeRunner.ts) or reusing Claude Codes stored OAuth token (unsupported/fragile). For the selection-assist translate feature the user chose to accept the ~12s floor rather than introduce an API key.

So: do not promise "blazing fast" for CLI-spawn features; the floor is ~12s. If speed matters, budget for a direct-API path from the start. Related: [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]].

## Related

- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]
