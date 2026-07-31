---
title: "luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership"
created: 2026-06-16
type: lesson
status: evergreen
source: "session 2026-06-16"
tags: [luz-docs, integration-test, behave, security-class, regression, gotcha]
---

# luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership

The 6 luz-docs IT security-class scenarios that failed on the `$SCn` branch but passed on master were **deterministic regressions**, not membership/visibility flakiness. An earlier theory — that `$SCn` resolved to classes with empty `usernames` so the admin token could not see them — was **disproven**: the admin/cron token visibility is governed by its `security_classes` claim, not by class `usernames` (every class on tenant b260a45a has empty `usernames`, yet its codes are visible).

Two independent regression classes, both introduced by commit `a72c785`:

1. **Dropped name->code map** (`update_patch_folder.feature:309/324/342`). `update_patch_folder_steps.py:_sc_code` was rewritten to placeholder-only `security_class_util.resolve(...)`, dropping the prior `context.security_class_codes.get(name, name)` fallback. Scenarios that kept seeded literal names (`SC1_4/SC2_2/SC3_3`, created by `ensure_security_classes`) then sent the *name* instead of the server *code* (`SC1_4`->`SC14_1`), so the bulk parent-move cascade 404d on folders with invalid codes. Fix: resolve `$SCn` first, then map any remaining literal name->code (the two namespaces are disjoint).

2. **Unresolved placeholder** (`search_document.feature:380/400`, `get_folder.feature:91`). The feature files were converted to `$SC1` but `search_document_steps.py` and `get_folder_steps.py` were never updated to resolve, so the literal string `"$SC1"` was sent as the folder security class -> authorized search/get failed deterministically. Fix: resolve via `security_class_util.resolve_csv(context.access_token, value)`.

Verified: after both fixes all 6 scenarios pass 2/2 in isolation, no read-after-write waits needed. Commits `dd1f153` (map) and `5e8103c` (resolution).

Lesson: when a "tenant-agnostic placeholder" conversion touches feature files, every consuming step definition must resolve the placeholder; a half-converted suite fails *deterministically* (invalid class strings), which is easy to misread as flaky.

Supersedes the disproven membership/visibility theory. Related: [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

## Related

- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
