---
title: "junit-platform-launcher needs an explicit version without a junit-bom"
created: 2026-06-08
type: lesson
status: seedling
source: "LEO CDP test-stage fix, 2026-06-08"
tags: [gradle, junit5, testing, gotcha, versions]
---

# junit-platform-launcher needs an explicit version without a junit-bom

When adding `testRuntimeOnly 'org.junit.platform:junit-platform-launcher'` to a project with NO junit-bom in dependency management, Gradle fails with 'Could not find org.junit.platform:junit-platform-launcher:.' (note the empty version after the colon) - it has no version to resolve. Projects that declare `junit-jupiter-engine:5.10.3` directly (no BOM) must ALSO pin the launcher explicitly: `junit-platform-launcher:1.10.3`. Version mapping: Jupiter 5.10.x <-> Platform 1.10.x (the platform minor trails the jupiter minor by 4: 5.x -> 1.(x). The clean alternative is importing `platform('org.junit:junit-bom:5.10.3')` and leaving both unversioned.

## Related

- [[Gradle 9 failOnNoDiscoveredTests exposes never-configured JUnit platform]]
