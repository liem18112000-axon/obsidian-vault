---
ai_hash: 86b97253961c4875
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities: []
source: LEO CDP CI/test-stage failure, 2026-06-07
status: seedling
tags:
- gradle
- gradle9
- junit5
- testing
- gotcha
title: Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform
type: lesson
---

# Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform

Gradle 9 added a `failOnNoDiscoveredTests` check: if test SOURCES exist but the test task discovers zero tests, :test now FAILS with 'There are test sources present and no filters are applied, but the test task did not discover any tests'. Legacy JUnit-5 projects that never called `useJUnitPlatform()` ran for years with silently-skipped tests (default JUnit4 detector finds nothing); on Gradle 9 that silence becomes a hard failure. Correct fix: `test { useJUnitPlatform() }` + `testRuntimeOnly 'org.junit.platform:junit-platform-launcher'` - which also means previously-never-run tests now actually execute (they may fail for missing infra; that is the truth surfacing, not a regression). Escape hatch (hides the misconfig - avoid): failOnNoDiscoveredTests=false.

## Related

- [[Gradle 9 forbids attributes() on declarable configurations in configurations.all]]

%% ai-graph-start %%

**Related notes:**
- [[junit-platform-launcher needs an explicit version without a junit-bom]]
- [[Wall of NoClassDefFoundError on first test run = static-init IO, split unit from integration]]
- [[JUnit5 @BeforeAll must be static - non-static masks every test in the class]]
- [[Gradle 9 forbids attributes() on declarable configurations in configurations.all]]
- [[Verify test files still exist on disk before trusting prior green test runs]]

%% ai-graph-end %%