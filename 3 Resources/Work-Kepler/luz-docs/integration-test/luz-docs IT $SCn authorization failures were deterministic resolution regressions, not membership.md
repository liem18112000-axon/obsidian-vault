---
title: "luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership"
created: 2026-06-16
type: lesson
status: evergreen
source: "sessions 2026-06-15 / 2026-06-16, dev tenant b260a45a"
tags: [luz-docs, integration-test, behave, security-class, regression, authorization, gotcha]
---

# luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership

Converting luz-docs IT security-class codes to `$SCn` placeholders (commit `a72c785`) made **6 authorization-visibility scenarios fail** on the branch while passing on master (dev, tenant `b260a45a`: master 484 passed / 2 failed, branch 478 passed / 7 failed). They were **deterministic regressions**, not visibility flakiness.

**Disproven theory (do not revisit):** that `$SCn` resolved alphabetically to classes with empty `usernames`, hiding them from the admin/cron token. Token visibility is governed by the token's `security_classes` claim, **not** by class `usernames` — every class on `b260a45a` has empty `usernames` yet its codes are visible. Seeding `usernames` membership is a no-op.

**Actual root causes — two independent classes:**

1. **Dropped name→code map** (`update_patch_folder.feature:309/324/342`). `update_patch_folder_steps.py:_sc_code` was rewritten to placeholder-only `security_class_util.resolve(...)`, dropping the `context.security_class_codes.get(name, name)` fallback. Scenarios still using seeded literal names (`SC1_4`/`SC2_2`/`SC3_3` from `ensure_security_classes`) sent the *name* instead of the server *code* (`SC1_4`→`SC14_1`), so the bulk parent-move cascade 404'd. Fix: resolve `$SCn` first, then map any remaining literal name→code (the namespaces are disjoint). Commit `dd1f153`.

2. **Unresolved placeholder** (`search_document.feature:380/400`, `get_folder.feature:91`). Feature files were converted to `$SC1` but `search_document_steps.py` and `get_folder_steps.py` were never updated to resolve, so the literal `"$SC1"` went to the server. Fix: `security_class_util.resolve_csv(context.access_token, value)`. Commit `5e8103c`.

Verified: all 6 pass 2/2 in isolation after both fixes, no read-after-write waits. The 7th branch failure (`update_patch_document:266` scalar-add) fails on master too — pre-existing, not a regression.

**Lesson:** when a tenant-agnostic-placeholder conversion touches feature files, *every* consuming step definition must resolve the placeholder. A half-converted suite fails **deterministically** with invalid class strings, which is easy to misread as flakiness or as an ACL/membership effect.

## Related

- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
- [[luz-docs IT before_scenario reads but does not substitute $SCn into step text]]
- [[behave step patterns differing only by quote style are distinct definitions]]
- [[scalar add to securityClassCodes tail is a known luz-docs IT pre-existing failure]]
