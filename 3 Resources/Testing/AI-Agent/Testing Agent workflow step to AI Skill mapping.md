---
title: "Testing Agent workflow step to AI Skill mapping"
created: 2026-08-26
type: model
status: seedling
source: "session 2026-08-26, beforeafterai.excalidraw"
tags: [testing-agent, ai-skills, bdd, polaris, vinnstack, qa]
---

# Testing Agent workflow step to AI Skill mapping

Maps each step of the "After AI" QA workflow (in beforeafterai.excalidraw) to the concrete Polaris/AI-First skills that execute it. The skill chain is the vinnstack "AI-First development pipeline" + "Testing Agent" set, living in `C:\Users\dvtliem\.claude\skills\*\SKILL.md`.

## Step → Skill

- **Knowledge gathering** → `interrogate-business` (R1) + `interrogate-technical` (R2) + `graphify-investigate` (map the codebase); context from Polaris Memory Bank + Agent Memory.
- **Knowledge refinement** (answer/clarify agent questions) → `interrogate-qa` (pre-BDD test-design questions).
- **Test Plan definition** (methodology API/E2E/UI, scope) → `interrogate-qa` + `review-testability` (shift-left QA gate on the PRD).
- **Test Plan implement** (test data, scenarios happy/negative, steps) → `story-to-bdd-scenarios` (Gherkin) + `write-acceptance-tests` (failing RED tests).
- **Test execution on env** (prepare → run → clean up → document) → `implement-bdd-steps` (behave step defs) + `luz-docs-integration-test` (API run) + `playwright-klara-earchive` (UI run).
- **Test Evaluation definition** (metrics, efficiency, coverage) → `review-testability`.
- **Test Evaluation support** (test monitoring) → `google-skill-gke-monitor` + `luz-skill-flow-logs`.
- **Test Completion Report** (results by category, tickets) → `write-test-completion-report` + `qa-html-before-delivery` (delivery gate).
- **Test triage** + **triage support** (categorize defect, raise ticket, record history) → `grounded-bug-report`.

## Three agents own the phases
- **Test AI Agent** — plan → implement → execute → report.
- **Ops AI Agent** — evaluation support / monitoring / fix support.
- **Report AI Agent** — triage + reporting.

The deterministic runner (behave/Playwright) decides pass/fail; the LLM only generates tests (pre-run) and triages failures (post-run) — "deterministic core, LLM at the edges".

## Related

- [[AI-First development pipeline]]
- [[story-to-bdd-scenarios]]
- [[write-test-completion-report]]
