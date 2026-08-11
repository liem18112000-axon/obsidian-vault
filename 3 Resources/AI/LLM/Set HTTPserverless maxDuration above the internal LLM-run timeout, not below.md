---
ai_hash: ca174e9359ececa3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack technical interrogation timeout)
status: seedling
tags:
- llm
- timeouts
- nextjs
- headless-claude
- vinnstack
- gotcha
title: Set HTTP/serverless maxDuration above the internal LLM-run timeout, not below
type: lesson
---

# Set HTTP/serverless maxDuration above the internal LLM-run timeout, not below

A headless LLM run that grounds in code via tools (Read/Grep/Glob) and generates diagrams can legitimately take **~5 minutes** over many turns — far longer than a single-shot text generation. Size timeouts to the *measured* wall-clock (e.g. a technical-interrogation run measured ~299s / 13 turns), not a hopeful round number.

**Ordering rule:** the outer request budget (Next.js route `export const maxDuration`, or any serverless/proxy limit) must sit **above** the inner per-run timeout, never below. If the inner run allows 480s but the route caps at 300s, the platform kills the request first and the inner timeout never gets a chance — the user just sees a generic failure.

Real case: Vinnstack Interrogation Room. The Business interrogation is fast, but the Technical one (grounds in the codebase + draws Mermaid) took ~299s and a flat 240s cap in `lib/interrogationRunner.ts` killed every technical generation just shy of finishing. Fix: track-aware timeout (technical 480s, business 300s) + raise the route `maxDuration` (300 -> 540) so it stays above the inner cap.

Diagnostic tip: run the CLI directly with `--output-format json` and read `duration_ms`, `num_turns`, `stop_reason` — that instantly distinguishes 'legitimately slow' (end_turn, high duration) from 'looping' (many turns, no progress).

%% ai-graph-start %%

**Related notes:**
- [[Long agentic API routes need the inner run timeout below the route maxDuration]]
- [[Technical interrogation questions must pass the 2-minute tech-lead test]]
- [[A stalled-looking test step may just be a long silent poll, not a hang]]
- [[Route tool-less LLM passes to a cheaper model tier]]
- [[State machines must catch expected-failure operations or they get stuck forever]]

%% ai-graph-end %%