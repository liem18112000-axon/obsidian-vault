---
ai_hash: e940b8eda5a09a53
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-13
entities:
- luz-docs IT create-document step
- server 500
- cryptic base_metadata AttributeError
- luz_docs_integration_test
- behave step create document
- create_document_steps.py
- context.status_code
- context.base_metadata
- 4xx status codes
- patch/get operations
- update_patch_document_steps.py
- _execute_patch
- _get_document_metadata
- 'AttributeError: Context object has no attribute base_metadata'
- POST /documents
- _require_base_metadata(context)
- create status code
- luz-docs RESTEASY003880 UriInfo 500 regression
- MaterializeRequestFilter
- async CDI events
- UriInfo
source: session 2026-06-12
status: seedling
tags:
- luz-docs
- integration-test
- behave
- python
- gotcha
title: luz-docs IT create-document step swallows server 500 and surfaces as cryptic
  base_metadata AttributeError
type: lesson
---

# luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError

In luz_docs_integration_test, the behave step `create document with "..." and "..." and "..."` (`features/steps/create_document_steps.py:step_create_document`) wraps the create call in `try/except` and on failure only sets `context.status_code` (for negative create tests) — it never sets `context.base_metadata`. That swallow is intentional for scenarios that assert a 4xx, but when the step is used purely as **setup** for a later patch/get, a server 500 leaves `base_metadata` absent.

The failure then surfaces two steps later in `update_patch_document_steps.py` (`_execute_patch` / `_get_document_metadata` read `context.base_metadata.get(...)`) as the misleading `AttributeError: Context object has no attribute base_metadata` — behaves Context raising on a missing attribute. So this "test bug" is really a downstream symptom of a server 500 on document creation, not an independent defect.

Honest test-repo fix: add a `_require_base_metadata(context)` guard in the patch helpers that fails fast with a clear message naming the create status code, instead of letting the cryptic AttributeError mask the real `POST /documents` 500. This does NOT make the scenario pass (the server bug remains) — it makes the failure point at the true cause.

Root server cause of the 500 in the 2026-06-12 run: [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]].

## Related

- [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]]
- [[luz-docs 2026-06-11 dev integration run failure clusters]]
- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]
- [[scalar add to securityClassCodes tail is a known luz-docs IT pre-existing failure]]
- [[Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)]]

**Relations:**
- luz-docs IT create-document step — *swallows* — server 500
- luz-docs IT create-document step — *surfaces_as* — cryptic base_metadata AttributeError
- behave step create document — *is_part_of* — luz_docs_integration_test
- behave step create document — *is_defined_in* — create_document_steps.py
- create_document_steps.py — *sets* — context.status_code
- create_document_steps.py — *fails_to_set* — context.base_metadata
- server 500 — *leads_to_absent* — context.base_metadata
- cryptic base_metadata AttributeError — *is_specific_error* — AttributeError: Context object has no attribute base_metadata
- AttributeError: Context object has no attribute base_metadata — *surfaces_in* — update_patch_document_steps.py
- _execute_patch — *accesses* — context.base_metadata
- _get_document_metadata — *accesses* — context.base_metadata
- cryptic base_metadata AttributeError — *masks* — server 500
- server 500 — *occurs_during* — POST /documents
- _require_base_metadata(context) — *is_proposed_fix_for* — cryptic base_metadata AttributeError
- _require_base_metadata(context) — *reports* — create status code
- luz-docs RESTEASY003880 UriInfo 500 regression — *is_root_cause_of* — server 500
- luz-docs RESTEASY003880 UriInfo 500 regression — *involves* — MaterializeRequestFilter
- luz-docs RESTEASY003880 UriInfo 500 regression — *involves* — async CDI events
- luz-docs RESTEASY003880 UriInfo 500 regression — *involves* — UriInfo
- luz-docs RESTEASY003880 UriInfo 500 regression — *is_related_to* — luz-docs IT create-document step
- luz-docs IT create-document step — *is_used_for* — setup
- luz-docs IT create-document step — *is_used_for* — patch/get operations
- create_document_steps.py — *handles* — 4xx status codes

%% ai-graph-end %%