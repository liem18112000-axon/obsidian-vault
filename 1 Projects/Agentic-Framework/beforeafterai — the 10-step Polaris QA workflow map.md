---
title: "beforeafterai — the 10-step Polaris QA workflow map"
created: 2026-08-27
type: model
status: seedling
source: "ai-agentic-framework/docs/beforeafterai.excalidraw — session 2026-08-27"
tags: [qa-workflow, polaris, skills, agents, luz-159471]
---

# beforeafterai — the 10-step Polaris QA workflow map

The `beforeafterai.excalidraw` (in `ai-agentic-framework/docs`) is the canonical **QA lifecycle map**: 10 steps, grouped into 6 phases, owned by 3 agents, each step executed by concrete Polaris AI **skills** (purple in the diagram).

**Phases → owning agent:** KNOWLEDGE, PLAN, EXECUTE → **Test AI Agent** · EVALUATE → Test + **Ops** · REPORT → **Report AI Agent** · TRIAGE → Report + Ops.

**The 10 steps → skills:**
1. Knowledge gathering — interrogate-business, interrogate-technical, graphify-investigate  (→ Polaris Memory Bank)
2. Knowledge refinement — interrogate-qa
3. Test Plan definition — interrogate-qa, review-testability
4. Test Plan implement — story-to-bdd-scenarios, write-acceptance-tests
5. Test execution — implement-bdd-steps, luz-docs-integration-test, playwright-klara-earchive
6. Test Evaluation definition — review-testability
7. Test Evaluation support (monitoring) — google-skill-gke-monitor, luz-skill-flow-logs
8. Test Completion Report — write-test-completion-report, qa-html-before-delivery
9. Test triage — grounded-bug-report
10. Test triage support — grounded-bug-report

**Data stores:** Jira/Xray, Confluence, **Polaris Memory Bank** (+ Agent Memory), Git, GCS (evidence). **Triggers:** Bitbucket PR/merge/nightly-cron → Cloud Build. The LUZ-159671 test-agent maps to **Step 1 (KNOWLEDGE)**; its GCS-markdown memory bank implements the Polaris Memory Bank.

## Related

- [[vinnstack SKILL.md convention]]
- [[Knowledge-Gathering loop is a bounded frontier crawl with a verify edge]]
