---
title: "luz-docs IT $SCn from the full token claim breaks authorization-visibility scenarios"
created: 2026-06-15
type: lesson
status: superseded
source: "session 2026-06-15"
tags: [luz-docs, integration-test, behave, security-class, gotcha, authorization]
---

# luz-docs IT $SCn from the full token claim breaks authorization-visibility scenarios

> [!warning] SUPERSEDED — the conclusion below is WRONG (disproven 2026-06-16)
> `$SCn` resolution did not "break" these scenarios via membership/visibility. The failures were **deterministic resolution regressions** (dropped name→code map + unresolved `$SC1` literals in two step files). Token visibility is governed by the `security_classes` claim, not by class `usernames`; seeding `usernames` is a no-op. See [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]].

When luz_docs_integration_test resolves `$SCn` against the access token's `security_classes` claim (sorted, 1-based), the claim of a cron / all-tenant token lists **every** tenant security-class code — not just the ones the calling user is a **member** of. Membership = the user appearing in the class's `usernames`.

**Consequence:** scenarios that exercise a *security-filtered read path* (search with security filtering, GET with ACL, a re-fetch after PATCH, bulk folder ops) fail when `$SCn` happens to resolve to a class the user is not a member of. The folder/doc exists and stores the code correctly, but is invisible to that user → search returns nothing, GET returns None/404, an added code is stripped on re-fetch.

**Observed (2026-06-15, tenant b260a45a):** 6 of 55 targeted scenarios failed for exactly this reason — search "authorized" (direct + inherited), get_folder "user match security class", update_patch_document scalar-add-to-tail, and two update_patch_folder bulk-move scenarios (404 failedEntries). The resolver itself was correct (`$SC1`→`SC14_6`); 49 scenarios passed. Structural checks (folder inheritance, materialize `_effectiveSecurityClassCodes`) pass because they verify stored/computed codes, not user visibility.

**Why the old hard-coded codes (THANHRIGHT_1/SC1_4) may have passed:** those specific named classes were likely set up on the test tenant with the user already a member; sorted-by-index resolution instead picks an arbitrary first code (e.g. SC14_6) with empty `usernames`.

**Fix options:** (1) seed the classes with the test user in `usernames` (in `ensure_security_classes` / `templates.json`) so the user is a member of $SC1..$SCn — most surgical; (2) resolve $SCn against a member-only claim if the token exposes one; (3) accept as known (note the search authorized/unauthorized pair is also an inherent contradiction — identical setup, opposite expectations).

Related: [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

## Related

- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
