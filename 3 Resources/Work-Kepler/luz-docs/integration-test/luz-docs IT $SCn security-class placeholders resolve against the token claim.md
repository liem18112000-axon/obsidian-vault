---
title: "luz-docs IT $SCn security-class placeholders resolve against the token claim"
created: 2026-06-15
type: concept
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, behave, security-class, gotcha]
---

# luz-docs IT $SCn security-class placeholders resolve against the token claim

In `luz_docs_integration_test` (behave), feature files and materialize JSON case files name security classes by 1-based placeholders `$SC1`, `$SC2`, … instead of hard-coded codes. Each resolves at runtime to the n-th entry of the tenant access token's `security_classes` claim, **sorted alphabetically**. This keeps the suite tenant-agnostic — it replaced commit `cae36b8`'s hard-coded `THANHRIGHT_1` / `SC1_4`, which only worked against one tenant.

**API — `core/util/security_class_util.py`:**
- `resolve(token, value)` — recurses a str / list / dict and substitutes every `$SCn`.
- `resolve_csv(token, "A,B,$SC1")` — for comma-separated step args.
- `token_security_classes(token)` — base64-decodes the JWT payload, returns the sorted claim.
- `max_placeholder_index(obj)` — highest `$SCn` used anywhere in obj (drives the skip guard).

**Non-placeholder passthrough (deliberate):** strings that are not `$SCn` (`A`, `B`, `NOT_MATCHING_SC`, `ADMIN`) pass through untouched, so deliberately-unauthorized literals stay guaranteed-unauthorized on any tenant.

`document_service.create_document` also resolves `$SCn` inside a metadata file's `securityClassCodes` (e.g. `materialize_document_metadata_with_security.json`), so doc-level security in test data is token-driven too. The tenant still seeds classes (`templates.json` for dynamic tenants, `ensure_security_classes` for static) so the token carries enough codes.

## Related

- [[luz-docs IT skips security-class scenarios when the tenant token lacks enough codes]]
- [[luz-docs IT before_scenario reads but does not substitute $SCn into step text]]
- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]
