---
title: "vinnstack BDD pipeline stops at Jira/Xray, never writes files into a cloned repo"
created: 2026-07-11
type: observation
status: seedling
source: "vinnstack session 2026-07-11: building implement-bdd-steps skill"
tags: [vinnstack, bdd, architecture, xray, jira]
---

# vinnstack BDD pipeline stops at Jira/Xray, never writes files into a cloned repo

vinnstack's own BDD feature (`lib/bdd/*`, `app/api/bdd/*`, `components/bdd/*`) is entirely Jira/Xray-based, not git-based: a Postgres draft store (`bddStore.ts`, tables `bdd_features`/`bdd_scenarios`/`bdd_scenario_revisions`) holds Claude-generated scenarios (via `bddRunner.ts`, which composes the `story-to-bdd-scenarios` / `interrogate-qa` markdown skills and runs Claude headlessly), and "approving" a scenario (`approveBddScenario()`) pushes it straight to Xray Cloud via `xrayClient.ts`'s `/import/feature` REST call — building a synthetic single-Scenario `.feature` file only in-memory, never on disk.

There is no code anywhere in vinnstack that clones a repo, writes `.feature`/step-definition files to disk, or pushes a branch/PR. That capability exists only inside `luz_docs_integration_test`'s own separate `helpers/ai/` pipeline (see [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]). If vinnstack's pipeline is ever extended with a "commit generated tests/steps into the target repo" stage, that stage has to be built from scratch — it cannot reuse an existing vinnstack module.

Also notable: vinnstack-skills (`story-to-bdd-scenarios`, `interrogate-qa`, and the new `implement-bdd-steps`) are pure markdown contract files with no accompanying scripts — all orchestration logic lives in the TypeScript runners (`lib/bdd/*Runner.ts`) that read and compose the skill text, not in the skill directories themselves.

Related: [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]

## Related

- [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate]]
- [[implement]]
- [[PR agents)]]
