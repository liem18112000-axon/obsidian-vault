---
title: "MaterializeGate migration check falls through to repo on missing campaign"
created: 2026-07-22
type: concept
status: seedling
source: "session 2026-07-22, commit 3a83b5e82"
tags: [luz-docs, materialize, gate, mockito]
---

# MaterializeGate migration check falls through to repo on missing campaign

MaterializeGate.isMaterializationComplete() treats a missing campaign record as a failed check, not a definitive false — it falls through to the actual repo check (repo.isMaterialized), same as a real campaign-service exception.

Commit 3a83b5e82 (LUZ-156856) changed the migration-check supplier from `.map(...).orElse(false)` to `.map(...).orElseThrow()`. When `campaignService.findBySubject()` returns null (no campaign record), `Optional.ofNullable(null).map(...)` is empty, and `.orElseThrow()` throws `NoSuchElementException`. The gate's `attempt()` wrapper catches any `RuntimeException` from a check and treats it as "this check didn't answer", moving on to the next check in the list (cache -> migration -> repo). So a missing campaign record and a broken campaign service now behave identically: both fall through to the repo check instead of the migration check returning a hard false.

Why: the intent (per commit message) is that "no campaign record" shouldn't be trusted as "not materialized" -- it should defer to the source of truth (the repo) rather than blocking materialization on a migration-tracking gap.

See [[Gate behavior changes must update tests asserting old fallthrough in the same commit]] for the process gotcha this caused.

## Related

- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[materialize design doc]]
