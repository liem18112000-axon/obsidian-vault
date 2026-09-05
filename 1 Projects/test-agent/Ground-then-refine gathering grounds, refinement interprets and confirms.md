---
title: "Ground-then-refine: gathering grounds, refinement interprets and confirms"
created: 2026-08-27
type: model
status: seedling
source: "session 2026-08-27 test-agent"
tags: [agentic-workflow, qa-agent, context-engineering, interrogation, test-agent]
---

# Ground-then-refine: gathering grounds, refinement interprets and confirms

A robust two-stage pattern for feeding an AI agent trustworthy context: separate **grounding** from **interpretation**, and put a human between them.

**Stage 1 — Gather (grounds).** Read-only crawl of the sources (Jira/Confluence/links/code/logs) into a distilled **context pack**: curated notes with provenance, a link graph, and *declared gaps*. Rule: **invent nothing, drop nothing silently** — an out-of-scope link is recorded, a broken one flagged, an unreachable node becomes a gap row, never an absent one. Grounding does NOT interpret.

**Stage 2 — Refine (interprets & confirms).** Turn the pack into a small, ranked set of **questions** (business → technical → QA rounds), **self-answering everything derivable** (recorded as vetoable assumptions) and surfacing only genuine judgement calls with options + a recommendation. The human answers; each confirmed answer is distilled into a **provenance-carrying "insight" note** (who answered, when, confidence) written back to the memory bank. The agent then **restates its understanding** in plain language for the human to confirm/correct.

The two stages form a **loop**: an answer that names a missing source becomes a new gather seed → re-ground → re-interrogate; terminate on a confidence bar / round budget, leftover questions declared as gaps. Net effect: the downstream planner reads *grounded facts + settled decisions + a confirmed understanding*, instead of guessing at the forks a raw crawl leaves open.

This maps onto the vinnstack `interrogate-business` / `interrogate-technical` / `interrogate-qa` skills as the three round engines, with the "dont ask what you can answer" and altitude rules carried over.

Source: test-agent Testing Agent, Step 1 (Knowledge Gathering) → Step 2 (Knowledge Refinement), LUZ-159671.

## Related

- [[A2A multi-turn human-in-the-loop via input-required Task state]]
