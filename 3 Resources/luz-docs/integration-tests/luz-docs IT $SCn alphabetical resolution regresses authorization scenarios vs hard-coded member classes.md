---
title: "luz-docs IT $SCn alphabetical resolution regresses authorization scenarios vs hard-coded member classes"
created: 2026-06-15
type: observation
status: superseded
source: "session 2026-06-15 dev IT comparison"
tags: [luz-docs, integration-test, behave, security-class, regression, authorization]
---

# luz-docs IT $SCn alphabetical resolution regresses authorization scenarios vs hard-coded member classes

> [!warning] SUPERSEDED — the conclusion below is WRONG (disproven 2026-06-16)
> The 6 failures were NOT a visibility/membership effect of `$SCn` resolution. They were **deterministic resolution regressions**: a dropped name→code map and unresolved `$SCn` placeholders in two step files, both sending invalid class strings. Admin/cron token visibility is governed by the token's `security_classes` claim, not by class `usernames`. The "seed membership" fix below is a no-op. See [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]].

A dev-vs-dev IT comparison (2026-06-15, tenant b260a45a) proved that converting hard-coded security-class codes to `$SCn` (resolved alphabetically from the token claim) **regressed 6 authorization-visibility scenarios** that pass on master.

master (dev): 484 passed, 2 failed (enrich:94, update_patch_document:266 scalar-add), 3 error (enrich).
this branch (dev): 478 passed, 7 failed, 3 error.

The 6 NEW failures on the branch (pass on master): search_document:380 + :400 (authorized search), get_folder:91 (get folder by matching security class), update_patch_folder:309/:324/:342 (bulk folder moves with security classes). The 7th failure (update_patch_document:266 scalar-add) fails on master too → pre-existing, not a regression (master used the non-class literal "DEV" -> []). The enrich failures/errors are flaky/environmental (different scenarios each run).

**Root cause:** master hard-codes THANHRIGHT_1/THANHLEFT_2/THANHTOP_3 — classes that on this tenant carry `usernames` membership granting the admin token visibility. `$SC1..$SC3` resolve alphabetically to SC14_6/SC22_10/SC33_11 (the SC1_4/SC2_2/SC3_3 classes) which have **empty `usernames`** → folders/docs secured by them are invisible to the admin token on ACL-filtered read paths (search, get-by-name, bulk folder ops). So sorted-by-index resolution is tenant-agnostic only for *storage/computation* assertions (which passed), not for *authorization-visibility* assertions, where the specific class must have membership.

**Implication:** the "sorted token claim by index" choice needs the resolved classes to also have the calling user as a member. Fix options: seed membership onto the classes $SCn lands on (replicating whatever THANHRIGHT_1 carries for the admin token), or resolve $SCn preferentially against member-classes, or run the visibility scenarios as a real end-user that is a member.

Related: [[luz-docs IT $SCn from the full token claim breaks authorization-visibility scenarios]] [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

## Related

- [[luz-docs IT $SCn from the full token claim breaks authorization-visibility scenarios]]
- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
