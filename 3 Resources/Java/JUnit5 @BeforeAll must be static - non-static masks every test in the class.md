---
title: "JUnit5 @BeforeAll must be static - non-static masks every test in the class"
created: 2026-06-08
type: lesson
status: seedling
source: "LEO CDP integration-test fix, 2026-06-08"
tags: [java, junit5, testing, gotcha, lifecycle]
---

# JUnit5 @BeforeAll must be static - non-static masks every test in the class

JUnit 5: @BeforeAll/@AfterAll methods MUST be static (unless the class is @TestInstance(Lifecycle.PER_CLASS)). A non-static @BeforeAll throws org.junit.platform.commons.JUnitException at discovery and counts the whole class as 1 failure - which also masks every @Test in it. In LEO CDP this bug sat dormant for years because the build never set useJUnitPlatform() so the tests never ran; Gradle 9 forced discovery and exposed it. Fixing (adding static to setup()+clean()) didn't just turn 3 reds green - it made the previously-hidden @Test methods actually execute (TestPostDataUtil 1->5 tests, all passing; the other two now run and fail on genuine data deps). Lesson: a class-level lifecycle error hides the real test count - always recount tests after fixing it.

## Related

- [[Wall of NoClassDefFoundError on first test run = static-init IO]]
- [[split unit from integration]]
