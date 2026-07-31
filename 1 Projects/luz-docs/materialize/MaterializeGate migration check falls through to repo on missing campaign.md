---
ai_hash: ed4ebc77b81e3967
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities:
- MaterializeGate
- MaterializeGate.isMaterializationComplete()
- missing campaign record
- failed check
- repo.isMaterialized
- campaign-service exception
- Commit 3a83b5e82 (LUZ-156856)
- migration-check supplier
- .map(...).orElse(false)
- .map(...).orElseThrow()
- campaignService.findBySubject()
- Optional.ofNullable(null).map(...)
- NoSuchElementException
- gate's attempt() wrapper
- RuntimeException
- check didn't answer
- next check
- cache check
- migration check
- repo check
- broken campaign service
- source of truth (the repo)
- Gate behavior changes must update tests asserting old fallthrough in the same commit
- materialize design doc
- MaterializeGate migration check falls through to repo on missing campaign
source: session 2026-07-22, commit 3a83b5e82
status: seedling
tags:
- luz-docs
- materialize
- gate
- mockito
title: MaterializeGate migration check falls through to repo on missing campaign
type: concept
---

# MaterializeGate migration check falls through to repo on missing campaign

MaterializeGate.isMaterializationComplete() treats a missing campaign record as a failed check, not a definitive false — it falls through to the actual repo check (repo.isMaterialized), same as a real campaign-service exception.

Commit 3a83b5e82 (LUZ-156856) changed the migration-check supplier from `.map(...).orElse(false)` to `.map(...).orElseThrow()`. When `campaignService.findBySubject()` returns null (no campaign record), `Optional.ofNullable(null).map(...)` is empty, and `.orElseThrow()` throws `NoSuchElementException`. The gate's `attempt()` wrapper catches any `RuntimeException` from a check and treats it as "this check didn't answer", moving on to the next check in the list (cache -> migration -> repo). So a missing campaign record and a broken campaign service now behave identically: both fall through to the repo check instead of the migration check returning a hard false.

Why: the intent (per commit message) is that "no campaign record" shouldn't be trusted as "not materialized" -- it should defer to the source of truth (the repo) rather than blocking materialization on a migration-tracking gap.

See [[Gate behavior changes must update tests asserting old fallthrough in the same commit]] for the process gotcha this caused.

## Related

- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[materialize design doc]]

%% ai-graph-start %%

**Related notes:**
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[Materialize tenant allowlist removed - cascade unconditional]]
- [[Migration campaign status can silently drift from real document state]]
- [[Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)]]
- [[Campaign flag now guards materialize write path (isAllowedTenant)]]

**Relations:**
- MaterializeGate — *has method* — MaterializeGate.isMaterializationComplete()
- MaterializeGate.isMaterializationComplete() — *treats* — missing campaign record
- missing campaign record — *as* — failed check
- MaterializeGate.isMaterializationComplete() — *falls through to* — repo.isMaterialized
- MaterializeGate.isMaterializationComplete() — *behaves like* — campaign-service exception
- Commit 3a83b5e82 (LUZ-156856) — *changed* — migration-check supplier
- migration-check supplier — *changed from* — .map(...).orElse(false)
- migration-check supplier — *changed to* — .map(...).orElseThrow()
- campaignService.findBySubject() — *returns null for* — missing campaign record
- Optional.ofNullable(null).map(...) — *is empty when* — campaignService.findBySubject() returns null
- .map(...).orElseThrow() — *throws* — NoSuchElementException
- gate's attempt() wrapper — *catches* — RuntimeException
- gate's attempt() wrapper — *treats* — RuntimeException
- RuntimeException — *as* — check didn't answer
- check didn't answer — *leads to* — next check
- missing campaign record — *falls through to* — repo check
- broken campaign service — *falls through to* — repo check
- missing campaign record — *and* — broken campaign service
- missing campaign record and broken campaign service — *behave identically* — fall through to repo check
- intent — *defers to* — source of truth (the repo)
- source of truth (the repo) — *for* — no campaign record
- Gate behavior changes must update tests asserting old fallthrough in the same commit — *is related to* — MaterializeGate migration check falls through to repo on missing campaign
- materialize design doc — *is related to* — MaterializeGate migration check falls through to repo on missing campaign
- gate's attempt() wrapper — *processes checks in order* — cache check, migration check, repo check
- cache check — *precedes* — migration check
- migration check — *precedes* — repo check

%% ai-graph-end %%