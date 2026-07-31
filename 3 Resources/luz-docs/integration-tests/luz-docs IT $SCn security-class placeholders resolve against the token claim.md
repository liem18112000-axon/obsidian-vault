---
title: "luz-docs IT $SCn security-class placeholders resolve against the token claim"
created: 2026-06-15
type: concept
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, behave, security-class, gotcha]
---

# luz-docs IT $SCn security-class placeholders resolve against the token claim

In the luz_docs_integration_test behave suite, feature files and materialize JSON case files refer to security classes by 1-based placeholders `$SC1`, `$SC2`, ... instead of hard-coded codes. Each placeholder resolves at runtime to the n-th entry of the tenant access token's `security_classes` claim, **sorted alphabetically** — so `$SC1` is the first sorted code the tenant actually holds.

**Why:** it makes the suite tenant-agnostic. Whatever codes a tenant holds get used and the specific names stop mattering. This replaced an earlier approach (commit cae36b8) that hard-coded real codes like `THANHRIGHT_1` / `SC1_4`, which only worked against one specific tenant.

**Where:** `core/util/security_class_util.py`:
- `resolve(token, value)` — walks a str / list / dict recursively and substitutes every `$SCn`.
- `resolve_csv(token, "A,B,$SC1")` — for comma-separated step args.
- `token_security_classes(token)` — base64-decodes the JWT payload and returns the sorted claim.
- `max_placeholder_index(obj)` — highest `$SCn` used anywhere in obj.

**Gotcha — non-placeholder passthrough:** strings that are not `$SCn` (e.g. `A`, `B`, `NOT_MATCHING_SC`, `ADMIN`) pass through untouched. This is deliberate: codes the user is NOT authorized for stay literal so they remain guaranteed-unauthorized regardless of tenant.

`document_service.create_document` also resolves `$SCn` inside a metadata file's `securityClassCodes`, so doc-level security in test-data files (e.g. `materialize_document_metadata_with_security.json`) is token-driven too.

The tenant still seeds classes (`templates.json` for dynamic tenants, `ensure_security_classes` for static) so the token carries enough codes to resolve the placeholders.

Related: [[luz-docs IT skips security-class scenarios when the tenant token lacks enough codes]]

## Related

- [[luz-docs IT skips security-class scenarios when the tenant token lacks enough codes]]
