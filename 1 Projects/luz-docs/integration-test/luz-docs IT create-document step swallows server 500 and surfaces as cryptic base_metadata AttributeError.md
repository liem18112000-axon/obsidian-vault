---
title: "luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError"
created: 2026-06-13
type: lesson
status: seedling
source: "session 2026-06-12"
tags: [luz-docs, integration-test, behave, python, gotcha]
---

# luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError

In luz_docs_integration_test, the behave step `create document with "..." and "..." and "..."` (`features/steps/create_document_steps.py:step_create_document`) wraps the create call in `try/except` and on failure only sets `context.status_code` (for negative create tests) — it never sets `context.base_metadata`. That swallow is intentional for scenarios that assert a 4xx, but when the step is used purely as **setup** for a later patch/get, a server 500 leaves `base_metadata` absent.

The failure then surfaces two steps later in `update_patch_document_steps.py` (`_execute_patch` / `_get_document_metadata` read `context.base_metadata.get(...)`) as the misleading `AttributeError: Context object has no attribute base_metadata` — behaves Context raising on a missing attribute. So this "test bug" is really a downstream symptom of a server 500 on document creation, not an independent defect.

Honest test-repo fix: add a `_require_base_metadata(context)` guard in the patch helpers that fails fast with a clear message naming the create status code, instead of letting the cryptic AttributeError mask the real `POST /documents` 500. This does NOT make the scenario pass (the server bug remains) — it makes the failure point at the true cause.

Root server cause of the 500 in the 2026-06-12 run: [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]].

## Related

- [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]]
