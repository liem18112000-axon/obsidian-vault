---
ai_hash: dc5865a9cef9dd4d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
entities: []
source: LEO CDP test-seeding, 2026-06-08
status: seedling
tags:
- testing
- integration-tests
- idempotency
- arangodb
- gotcha
title: Measure non-idempotent integration tests on clean state - 409 on re-run is
  an isolation defect
type: lesson
---

# Measure non-idempotent integration tests on clean state - 409 on re-run is an isolation defect

Non-idempotent integration tests must be measured on a CLEAN DB state, not by re-running. In LEO CDP, re-running the suite gave 56/66 with two '409 unique constraint violated' failures (CrudSegmentTest, TestUserDataUtil) - the tests insert fixed-key docs and have no teardown, so the SECOND run collides with the first run's data. Truncating all non-system collections + re-seeding default data + re-bootstrapping the super-admin, then running ONCE, gave the fair 59/66 (those 409s passed). Lesson: a 409/duplicate-key failure that appears only on re-run is a test-isolation defect (missing @AfterEach cleanup or random keys), NOT a code bug - judge such suites by their clean-state first-run, and fix isolation before wiring them into CI. The remaining failures after a clean run (missing per-test fixtures, async Redis pub/sub timing) are the genuinely test-specific work.

## Related

- [[JUnit5 @BeforeAll must be static - non-static masks every test in the class]]

%% ai-graph-start %%

**Related notes:**
- [[JUnit5 @BeforeAll must be static - non-static masks every test in the class]]
- [[Wall of NoClassDefFoundError on first test run = static-init IO, split unit from integration]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[Shape-keyed test mocks break when production query shapes change]]
- [[LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first]]

%% ai-graph-end %%