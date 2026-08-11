---
ai_hash: 6a725d2972ac728e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: 'vinnstack session 2026-07-11: building implement-bdd-steps skill'
status: seedling
tags:
- bdd
- cucumber
- behave
- ai-pipeline
- luz-docs-integration-test
title: luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement,
  PR agents)
type: concept
---

# luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)

luz_docs_integration_test (the behave/Cucumber IT suite for luz-docs) already contains a full multi-agent AI pipeline under `helpers/ai/`, on branch `ai-feature-generation` (not yet merged to master as of 2026-07-11) — built on Gemini/Vertex AI (ReAct agents), separate from any vinnstack-side automation.

Pipeline stages, chained by `OrchestratorAgent`:
- `TestGeneratorAgent` — Jira ticket key -> Gherkin scenarios -> imports them into an Xray Test Set.
- `StepImplementerAgent` — Xray Test Set key -> writes Python behave step definitions -> runs `behave` -> self-corrects/retries until 100% pass.
- `PRCreator` — bundles the generated files into a single BitBucket PR.

CLI entry point: `python -m helpers.ai.service {generate|implement|full} <KEY>` (`generate` takes a Jira issue key, `implement` takes an Xray Test Set key, `full` runs both). Runnable locally or as a GKE Job (`cloudbuild-ai.yaml` + `gke/k8s-ai.yaml`).

Relevant when designing any new "generate BDD tests" or "implement BDD steps" automation for this org — this pipeline is the working reference implementation to mirror conventions from, even if the new automation (e.g. a Claude-based vinnstack skill) uses a different LLM/orchestration layer.

Related: [[luz_docs_integration_test AI pipeline branch and PR mechanics]], [[luz_docs_integration_test Gherkin and step-definition conventions]], [[3 Resources/Work-Side/Vinnstack/vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]

## Related

- [[luz_docs_integration_test AI pipeline branch and PR mechanics]]
- [[luz_docs_integration_test Gherkin and step-definition conventions]]
- [[vinnstack BDD pipeline stops at Jira/Xray]]
- [[never writes files into a cloned repo]]

%% ai-graph-start %%

**Related notes:**
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]
- [[luz_docs_integration_test AI pipeline branch and PR mechanics]]
- [[luz_docs_integration_test Gherkin and step-definition conventions]]
- [[Vinnstack ai-framework.html is aspirational, not the real code]]
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]

%% ai-graph-end %%