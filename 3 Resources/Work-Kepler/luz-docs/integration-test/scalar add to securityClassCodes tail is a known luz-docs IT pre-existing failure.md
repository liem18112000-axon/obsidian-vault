---
ai_hash: c9d2dfef90bb491b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: dev IT run d833dbe 2026-06-16
status: seedling
tags:
- luz-docs
- integration-test
- behave
- gotcha
- security-class
title: scalar add to securityClassCodes tail is a known luz-docs IT pre-existing failure
type: lesson
---

# scalar add to securityClassCodes tail is a known luz-docs IT pre-existing failure

In the **luz_docs_integration_test** behave suite, the scenario **"Update patch document :: scalar add to securityClassCodes tail is accepted"** (`features/document/update_patch/update_patch_document.feature:266`) is a **known pre-existing backend failure**, not a test regression. When judging IT green/red, treat this one as out-of-scope.

## What it does
Patches a document with `add "$SC1" at tail` — a JSON-Patch `add` op on pointer `/securityClassCodes/-` — then asserts the document's `securityClassCodes` contains the class. The backend returns `[]` instead, so the assertion fails:

```
Then verify document securityClassCodes contains "$SC1":
  ASSERT FAILED: Expected securityClassCodes to contain 'SC14_6' but got []
```

## Why it's not our bug
- The `$SC1` placeholder **resolves correctly** (e.g. to `SC14_6` for cron tenant `b260a45a-70b9-4926-9894-d34e0d8906a8`), so the `$SCn` security-class conversion work is fine.
- Root cause is **server-side**: luz-docs does not persist a scalar `add` to the `securityClassCodes` tail.
- Confirmed failing on **master** and on branch `kepler/sprint-158/fix-IT-after-materialize-pattern` — dev IT run on commit `d833dbe` (2026-06-16) was **488 scenarios passed, this 1 failed**.

Relates to the `$SCn` security-class IT conversion and the materialize-pattern IT fix work.

## Related

- [[luz-docs integration test]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]
- [[securityClassCodes scalar string breaks materialize sentinels]]
- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]
- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]

%% ai-graph-end %%