---
title: "Set HTTP/serverless maxDuration above the internal LLM-run timeout, not below"
created: 2026-07-01
type: lesson
status: seedling
source: "session 2026-07-01 (Vinnstack technical interrogation timeout)"
tags: [llm, timeouts, nextjs, headless-claude, vinnstack, gotcha]
---

# Set HTTP/serverless maxDuration above the internal LLM-run timeout, not below

A headless LLM run that grounds in code via tools (Read/Grep/Glob) and generates diagrams can legitimately take **~5 minutes** over many turns — far longer than a single-shot text generation. Size timeouts to the *measured* wall-clock (e.g. a technical-interrogation run measured ~299s / 13 turns), not a hopeful round number.

**Ordering rule:** the outer request budget (Next.js route `export const maxDuration`, or any serverless/proxy limit) must sit **above** the inner per-run timeout, never below. If the inner run allows 480s but the route caps at 300s, the platform kills the request first and the inner timeout never gets a chance — the user just sees a generic failure.

Real case: Vinnstack Interrogation Room. The Business interrogation is fast, but the Technical one (grounds in the codebase + draws Mermaid) took ~299s and a flat 240s cap in `lib/interrogationRunner.ts` killed every technical generation just shy of finishing. Fix: track-aware timeout (technical 480s, business 300s) + raise the route `maxDuration` (300 -> 540) so it stays above the inner cap.

Diagnostic tip: run the CLI directly with `--output-format json` and read `duration_ms`, `num_turns`, `stop_reason` — that instantly distinguishes 'legitimately slow' (end_turn, high duration) from 'looping' (many turns, no progress).
