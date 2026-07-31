---
title: NVIDIA Vietnam SDET Interview Prep
date: 2026-07-13
tags:
  - interview
  - career
  - nvidia
status: active
aliases:
  - Interview point
---

# NVIDIA SDET Interview — Quick Cheat Sheet

> [!info] Logistics
> - **When:** Mon Jul 13, 2026 · 15:30 ICT
> - **Interviewer:** Hai Nguyen (product background → asks "why" a lot)
> - **HR:** Anh Tran · 0973 163 520
> - **Where:** MS Teams, join 5–10 min early

> [!tip] Golden rule
> If a "why" surprises you: say the trade-off out loud, even half-formed. Beats a guess dressed as certainty.
> Pick 3 stories below, know them cold. You don't need all of them.

## Process diagrams

Excalidraw source + rendered PNGs, saved in `doc/` next to the source HTML (not in this vault):

- **interview-adlc-flow** — 2 human gates, AFK/HITL branch (`doc/interview-adlc-flow.excalidraw`)
- **interview-bdd-pipeline** — 8-stage zigzag showing the AI-authors / deterministic-ships boundary (`doc/interview-bdd-pipeline.excalidraw`)
- **interview-earchive-architecture** — materialize + parallelize as parallel subsystems converging on the sub-2s latency bar (`doc/interview-earchive-architecture.excalidraw`)

![[interview-adlc-flow.png]]
![[interview-bdd-pipeline.png]]
![[interview-earchive-architecture.png]]

## Story 1 — ADLC: 2 human gates, not 10

**One line:** Redesigned the dev lifecycle so AI agents do the work, humans only approve the spec and the PR.

- **Problem:** Old SDLC has no place for AI agents without losing human control.
- **Fix:** Same phases, but only **2 hard gates** — approve the plan, approve the PR.
- Refine = 2 rounds: business questions first, then engineering questions.
- Split work into slices → **AFK** (agent alone) only if: small blast radius + no API/security change + fully spec'd + test seam exists. Anything else → **HITL**.
- One agent writes tests, a *different* agent writes code — no grading your own homework.
- AI reviews the PR first; human makes the call.

> [!question]- Quick why-answers
> - **Only 2 gates, risky?** → those are the only 2 decisions that matter: what to build, is it right.
> - **Checklist not gut-feel?** → stops drift, defaults to safe (HITL) when unsure.
> - **Why separate test-agent/code-agent?** → no self-grading.
> - **Why business round before engineering round?** → lock scope before debating how.
> - **Why a code graph instead of just reading files?** → real answers, not guesses.
> - **Why shared memory across runs?** → don't relearn the same facts twice.
> - **Why release is NOT in scope?** → different team, different gate.
> - **AFK task fails bar mid-build?** → drops back to HITL.
> - **Why AI reviews PR first?** → human checks triaged findings, not raw diff.

## Story 2 — BDD pipeline: 8 stages, Jira to PR

**One line:** Story → QA gate → Gherkin → Xray → AI writes test code → PR → independent verify.

- Own Jira client (not Atlassian MCP — it can miss the right site).
- **QA gate before any test is written**, checked twice (UI + server) — catches cross-story clashes one story alone can't see.
- Every test scenario must trace to a real AC, PRD, or code fact — never invented.
- AI writes step code with **no git/gh access** — it can prove tests pass, but can't ship them.
- Retry loop is capped — reports the blocker instead of looping forever.
- **Verify** is a separate stage from the AI's own test run — repeatable, catches flakiness.

> [!question]- Quick why-answers
> - **Why not Atlassian MCP?** → can silently point at the wrong site.
> - **Why block git/gh from the AI?** → can't misuse a tool it doesn't have.
> - **Why Verify separate from Implement's own run?** → "passed once" ≠ "still passes now."
> - **Why gate on client AND server?** → client-only checks can be bypassed.
> - **Why must scenarios trace to something real?** → invented tests check assumptions, not the system.
> - **Why cap the AI's retry loop?** → a wrong assumption never converges; report it instead.
> - **Why QA gate scoped to the whole batch, not one story?** → cross-story bugs are invisible one at a time.

## Story 3 — eArchive: materialize + parallelize

**One line:** Folder views were slow at 128k+ docs — pre-computed fields plus sharded counting made them fast.

- **Problem:** every folder view did live joins for security; counting = full scan. Painful past ~128k docs, tested up to ~800k.
- **materialize:** cache 3 fields on each doc — effective security codes, `_isPublic`, folder names.
- Folder rename/move fires an async update, done carefully (write a marker first, so a crash can recover later).
- **2 separate rollout switches:** "should this tenant use it" + "is backfill done yet" — not one flag.
- **parallelize:** random shard id per doc at creation → count splits into up to 64 parallel mini-counts instead of one big scan.
- **Bar hit:** folder/list views under 2 seconds, proven, with a check that unrelated pages didn't get slower.

> [!question]- Quick why-answers
> - **Why pre-compute instead of compute on read?** → live joins don't scale to 800k docs.
> - **Why 2 rollout flags instead of 1?** → "eligible" and "actually ready" are different questions.
> - **Why is counting its own separate system?** → different problem, different risk — don't couple them.
> - **How did you prove it's actually faster?** → measured against real large-folder data, checked nothing else got slower.
> - **Why does `_isPublic` need 2 conditions, not 1?** → a folder can restrict a doc even if the doc itself has no restriction.
> - **Why write the crash-recovery marker BEFORE the update runs?** → so a crash mid-way still leaves a trail to recover from.

## QA fundamentals — say these words

| Concept | Say this |
|---|---|
| 3 outcomes, not 2 | Pass / fail / **infra error** — don't blame the product for a missing `kubectl`. |
| Pure functions | Pulled logic out of I/O code so it's testable without Docker/gcloud. |
| Edge case > happy path | Tested which of two ID patterns "wins" — the one bug that'd silently break polling. |
| Preflight checks | Check Docker/gcloud/venv *before* a run, not after it fails. |
| Timeout + cleanup | Killing a wrapper process isn't enough — kill the whole process tree. |
| Flaky-test causes | Order dependency + duplicate step definitions. Catch before merge, not in CI. |
| Confounds | Dev-mode compile time, React Strict Mode double-calls, human delay vs. cache TTL — all can fake "no improvement." |
| Honest scope-out | Deferred cache tests, named it as a follow-up — not a silent gap. |
| 2-stage rollout | "Eligible" ≠ "ready." Two flags, two questions. |
| Crash recovery tested | Simulated a mid-update crash, proved it recovers. |
| Partial success is its own case | Mongo's 207 (partially applied) tested separately from pass/fail. |

```
$ npx vitest run test/lib/bdd test/lib/interrogation
112 passed | 36 skipped (no test DB) | 0 failed
```

## Mock Q&A — one-line answers

> [!question]- Bug you found in an unexpected place?
> Suspected the same bug twice, tested instead of assuming — second time, no bug. Lesson: test the hypothesis, don't pattern-match a past fix.

> [!question]- What do you choose NOT to test?
> Deferred cache tests (no test pattern existed yet) — named as follow-up, not hidden.

> [!question]- A time your first theory was wrong?
> "No improvement" was actually a cache TTL expiring between manual test steps — not a bug. Proved it with back-to-back API calls.

> [!question]- How do you prioritize testing with limited time?
> By risk — payment/auth/data get more than the standard floor; low-risk gets the floor. Make the trade-off visible.

> [!question]- What causes flaky tests?
> Order dependency + duplicate steps across files. Catch it by reviewing a whole batch together, not one at a time.

> [!question]- Testing something that needs Docker/gcloud?
> Fail fast with a real reason (not just "unavailable"), and separate "test failed" from "environment wasn't ready."

> [!question]- Why should an SDET care about performance work?
> A slowdown is a bug — measure before claiming a fix, watch for confounds, keep the suite green.

> [!question]- Ideal collaboration with a PM?
> Bring judgment calls, not busywork — let them weigh in on risk, not on "did you test the happy path."

> [!question]- A subtle root cause you found?
> A framework's own thread-locals got corrupted by firing an event too early — fixed by moving it later, found by tracing, not guessing.

> [!question]- How do you roll out a risky migration safely?
> Two flags (eligible / ready), canary first, safe to re-run, and a background sweep to clean up anything left half-done.

## AI tools — how I use them (lead with the *why*)

> [!quote] Core narrative — open with this if asked "your experience with AI tools"
> I use AI as an **accelerator with a human review gate — never a final verdict**. In my Java / Jakarta REST / MongoDB work I've used it for **test generation** and **code-review assistance**, but I always validate output against a **known-good baseline** before trusting it in a pipeline.

**Four answers — fire without hesitation** (say what it *solves* before the mechanism):
- **Test generation** → edge cases derived from diffs, **human-reviewed before merge**.
- **Self-healing locators** → fast to adopt, but **flag auto-healed matches — don't trust silently**.
- **Defect triage / clustering** → LLM clusters failures by pattern; **human confirms root cause**.
- **Where I *don't* trust AI** → security / compliance assertions, and anything with **no golden-set validation step**.

> [!warning] Curveball — "a limitation of LLMs you've personally run into"
> Have ONE concrete, specific moment ready — not "hallucination is a risk". Shape: *an AI-generated test that passed but asserted the wrong condition*, or *a self-healing locator that matched the wrong element*. Concrete beats abstract every time.

> [!tip] Delivery
> Lead with the **why**, not the **what**. Asked "how would you use AI for X?" → state what it solves first, then the mechanism.

Reusable principle: [[AI as an accelerator with a human review gate]].

## JD fit — quick hooks

*Python SWQA/SDET, AI Giant Tech Co., SEA R&D center (founding team)*

| JD asks | Your real anchor |
|---|---|
| Test matrix from requirements | QA interrogation gate — reads the whole story batch, not one story at a time |
| Test plan, design, execute, report | The 8-stage BDD pipeline, end to end |
| Automate tests, build test frameworks | Step implementation + `behave` runner + Vitest suite (112 passing) |
| Bug lifecycle, cross-team | `classifyItResult` 3-outcome split; flaky-test root causes named and fixed |
| Repro/verify customer issues | eArchive perf work (LUZ-156772) — real customer-scale bug, root cause, fix, proof |
| **Python** (3+ yrs, hard requirement) | `behave` **is** Python — venv setup, `import behave` preflight probe, the whole step-implementation stack runs on it |
| Shell/Unix (nice-to-have) | `taskkill /t /f` process-tree cleanup; bash entrypoints for local test runs |
| AI dev tools — generate tests, automate, code coverage (nice-to-have) | This **is** the ADLC/BDD pipeline: agents generating and proving test cases against acceptance criteria at scale |
| LLM benchmarking / model testing (nice-to-have) | Not done directly — be honest, then pivot: "the discipline transfers — measure before claiming, prove green with repeatable runs, catch flakiness before trusting a result" |

> [!tip] If Python depth gets probed
> Name `behave`, venv/`import behave` preflight, and the Python-side test execution honestly as your Python surface — don't overclaim framework-building in Python if most of your own code is TS/Java.

## Before the call

> [!todo] 15 min before
> Teams link ready · mic/camera checked · this note open

> [!info] If something breaks
> Call Anh Tran (HR) · 0973 163 520
