---
ai_hash: ab3f50f6698c26c8
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
entities: []
source: LEO CDP CI test diagnosis, 2026-06-08
status: seedling
tags:
- java
- testing
- static-initializer
- integration-tests
- migration
- gotcha
title: Wall of NoClassDefFoundError on first test run = static-init I/O, split unit
  from integration
type: lesson
---

# Wall of NoClassDefFoundError on first test run = static-init I/O, split unit from integration

Pattern when a Java migration suddenly makes a long-passing test suite fail with a wall of NoClassDefFoundError 'Could not initialize class X': it usually is NOT your code change. Two compounding causes seen in LEO CDP on Gradle 9: (1) the suite never actually ran before (no useJUnitPlatform -> JUnit5 tests silently skipped); Gradle 9's failOnNoDiscoveredTests forced them to run for the first time. (2) Those tests are integration probes whose model classes have **static initializers that open DB connections / read config at class-load** - they throw NoClassDefFoundError the instant they're touched without live infra. Diagnosis tell: the failure is 'Could not initialize class' (static-init), not an assertion failure. Fix = unit/integration split (default `test` includes only infra-free classes via filter{includeTestsMatching}, full set in a separate `integrationTest` task), not chasing 47 'failures'. Design lesson it exposes: heavy static initializers that touch I/O make classes untestable in isolation.

## Related

- [[Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform]]

%% ai-graph-start %%

**Related notes:**
- [[JUnit5 @BeforeAll must be static - non-static masks every test in the class]]
- [[LEO CDP SYSTEM_ENV_VARS still requires database-configs.json to exist first]]
- [[Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform]]
- [[Measure non-idempotent integration tests on clean state - 409 on re-run is an isolation defect]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]

%% ai-graph-end %%