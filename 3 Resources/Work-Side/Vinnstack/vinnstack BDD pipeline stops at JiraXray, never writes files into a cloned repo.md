---
ai_hash: ac3b06d472fa7da1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: 'vinnstack session 2026-07-11: building implement-bdd-steps skill'
status: seedling
tags:
- vinnstack
- bdd
- architecture
- xray
- jira
title: vinnstack BDD pipeline stops at Jira/Xray, never writes files into a cloned
  repo
type: observation
---

# vinnstack BDD pipeline stops at Jira/Xray, never writes files into a cloned repo

vinnstack's own BDD feature (`lib/bdd/*`, `app/api/bdd/*`, `components/bdd/*`) is entirely Jira/Xray-based, not git-based: a Postgres draft store (`bddStore.ts`, tables `bdd_features`/`bdd_scenarios`/`bdd_scenario_revisions`) holds Claude-generated scenarios (via `bddRunner.ts`, which composes the `story-to-bdd-scenarios` / `interrogate-qa` markdown skills and runs Claude headlessly), and "approving" a scenario (`approveBddScenario()`) pushes it straight to Xray Cloud via `xrayClient.ts`'s `/import/feature` REST call — building a synthetic single-Scenario `.feature` file only in-memory, never on disk.

There is no code anywhere in vinnstack that clones a repo, writes `.feature`/step-definition files to disk, or pushes a branch/PR. That capability exists only inside `luz_docs_integration_test`'s own separate `helpers/ai/` pipeline (see [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]). If vinnstack's pipeline is ever extended with a "commit generated tests/steps into the target repo" stage, that stage has to be built from scratch — it cannot reuse an existing vinnstack module.

Also notable: vinnstack-skills (`story-to-bdd-scenarios`, `interrogate-qa`, and the new `implement-bdd-steps`) are pure markdown contract files with no accompanying scripts — all orchestration logic lives in the TypeScript runners (`lib/bdd/*Runner.ts`) that read and compose the skill text, not in the skill directories themselves.

Related: [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]

## Related

- [[3 Resources/Work-Kepler/luz-docs/integration-test/luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]
- [[Vinnstack ai-framework.html is aspirational, not the real code]]
- [[Vinnstack vinnstack-data-model.html predates the BDD workspace]]
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]

%% ai-graph-end %%