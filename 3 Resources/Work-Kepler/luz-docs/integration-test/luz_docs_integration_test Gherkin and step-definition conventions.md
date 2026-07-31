---
ai_hash: c6ac5039c6c4b31e
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
- gherkin
- conventions
- luz-docs-integration-test
title: luz_docs_integration_test Gherkin and step-definition conventions
type: concept
---

# luz_docs_integration_test Gherkin and step-definition conventions

The `luz_docs_integration_test` repo documents its own Gherkin/behave conventions in `helpers/ai/prompts/templates/feature_conventions.md` and `step_conventions.md` — these are the authoritative, already-in-production rules for any BDD scenario or step implementation written for this repo (by AI or by hand):

**Feature files** (`feature_conventions.md`):
- Feature-level tags: exactly two, `@<action> @<resource>` (e.g. `@create @document`).
- Scenario-level tag: one hyphenated tag, `@<action>-<resource>` (e.g. `@create-document`).
- Every feature opens with a fixed `Background:` (`Given a valid account` / `And get access token`) — these steps already exist; never redefine them.
- Prefer `Scenario Outline:` + `Examples:` (must include a `testCase` column) over repeated plain `Scenario:` blocks.
- Coverage floor per feature: happy path, edge cases, error cases (401/400/404/403), boundary conditions.
- File location/naming: `features/<resource>/<action>/<action>_<resource>.feature`, lowercase underscored.

**Step definitions** (`step_conventions.md`):
- Live at `features/steps/<action>_<resource>_steps.py`, `from behave import given, when, then, step`.
- Decorator text must match the Gherkin step character-for-character or behave reports "undefined step."
- `context` carries `app_config` (service URLs), `access_token`, `current_tenant_id`, `current_username`/`current_password`, `created_documents`/`created_document_groups` (cleanup tracking lists), `status_code` (set in `except HTTPError` blocks so error-case scenarios can assert on it).
- Service calls go through existing service wrapper modules (e.g. `document_service.create_document(url, token, tenant_id, ...)`), not raw `requests` calls.
- When a step dispatches behavior off a parameter value, use a module-level `OPTION_HANDLERS` / `VERIFICATION_HANDLERS` dict instead of an if/elif chain — established project convention.
- Never re-implement a step that already exists elsewhere in `features/steps/`.

Related: [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]

## Related

- [[3 Resources/Work-Kepler/luz-docs/integration-test/luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs_integration_test has its own AI-driven BDD pipeline (generate, implement, PR agents)]]
- [[luz_docs_integration_test AI pipeline branch and PR mechanics]]
- [[JSON-driven Scenario Outline pattern for luz_docs materialize integration tests]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]
- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

%% ai-graph-end %%