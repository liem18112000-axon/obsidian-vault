---
title: "Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform"
created: 2026-06-07
type: lesson
status: seedling
source: "LEO CDP CI/test-stage failure, 2026-06-07"
tags: [gradle, gradle9, junit5, testing, gotcha]
---

# Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform

Gradle 9 added a `failOnNoDiscoveredTests` check: if test SOURCES exist but the test task discovers zero tests, :test now FAILS with 'There are test sources present and no filters are applied, but the test task did not discover any tests'. Legacy JUnit-5 projects that never called `useJUnitPlatform()` ran for years with silently-skipped tests (default JUnit4 detector finds nothing); on Gradle 9 that silence becomes a hard failure. Correct fix: `test { useJUnitPlatform() }` + `testRuntimeOnly 'org.junit.platform:junit-platform-launcher'` - which also means previously-never-run tests now actually execute (they may fail for missing infra; that is the truth surfacing, not a regression). Escape hatch (hides the misconfig - avoid): failOnNoDiscoveredTests=false.

## Related

- [[Gradle 9 forbids attributes() on declarable configurations in configurations.all]]
