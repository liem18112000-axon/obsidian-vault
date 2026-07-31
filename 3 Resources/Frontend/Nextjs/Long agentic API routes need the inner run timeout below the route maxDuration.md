---
ai_hash: d60671a5e2e95a46
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
entities: []
tags:
- nextjs
- api-routes
- timeout
- claude-code
- agents
- gotcha
---

# Long agentic API routes need the inner run timeout below the route maxDuration

When a Next.js API route spawns a long headless `claude` run, there are TWO timeouts and they must be ordered deliberately:

1. **The inner run timeout** — a `setTimeout` that SIGKILLs the child and rejects the promise (so a truncated/half-finished write is treated as an indeterminate failure, never trusted).
2. **The route `export const maxDuration`** — the outer request cap.

**Rule:** inner timeout < route maxDuration. If the inner timeout is *larger* (or the route cap is too small), the platform cuts the request off first and you never get the clean rejection/verification. If the inner timeout is too *small*, a legitimately long run gets SIGKILLed mid-work.

**Concrete gotcha (Vinnstack PRD approve):** the "approve PRD" write-back posts an Epic comment AND creates one Jira Story per slice over many MCP turns — a multi-Story PRD routinely runs 4–8 min. A 300 000 ms (5 min) inner cap SIGKILLed it right after a run that had *just* squeaked in at 256 s, producing an opaque `400 … timed out after 300000ms`. Fix: raise the inner cap to 780 s and the route `maxDuration` to 900 s. Symptom of "it's stuck" was actually "it's hitting a too-tight timeout, variably."

**Notes:** for a locally-run / self-hosted Next server, `maxDuration` isn't hard-enforced like serverless, but keep the ordering anyway for portability. Browsers put no default timeout on a `fetch`, so a client waiting on a 13-min localhost call won't self-abort — a manual cancel needs an explicit `AbortController`. Log the timeout branch (epic, kind, elapsed) so a variable timeout is diagnosable rather than looking like a hang.

Related: [[Claude Code runs on Vertex AI via three env vars with gcloud ADC]].

%% ai-graph-start %%

**Related notes:**
- [[Set HTTPserverless maxDuration above the internal LLM-run timeout, not below]]
- [[Headless claude exit 0 does not mean the operation succeeded]]
- [[Unattended AI pipeline - auto-advance to the next human gate inside a night-shift window]]
- [[A globally-bootstrapped MCP server loads into every headless claude spawn]]
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]

%% ai-graph-end %%