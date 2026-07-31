---
title: "luz_docs IT: $SCn security-class placeholders resolve against the tenant token claim"
created: 2026-06-15
type: concept
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, integration-test, behave, security-class]
---

# luz_docs IT: $SCn security-class placeholders resolve against the tenant token claim

Integration tests in luz_docs_integration_test (behave) refer to security-class codes by 1-based placeholders `$SC1`, `$SC2`, ... instead of hard-coded codes like `THANHRIGHT_1`. At runtime each placeholder resolves to the n-th entry of the tenant access token's `security_classes` claim, sorted for a stable mapping, via `core/util/security_class_util.py` (`resolve`, `resolve_csv`, `token_security_classes`, `max_placeholder_index`).

Why: it keeps scenarios tenant-agnostic — whatever codes a tenant actually holds get used, and the specific code names stop mattering. A step resolves placeholders by calling `security_class_util.resolve(context.access_token, value)` on a string / list / dict (recurses).

Strings that are not placeholders (e.g. a deliberately-unauthorized literal like `A`) pass through untouched.

Related: [[luz_docs IT auto-skips a scenario when the tenant token lacks enough security classes]]

## Related

- [[luz_docs IT auto-skips a scenario when the tenant token lacks enough security classes]]
