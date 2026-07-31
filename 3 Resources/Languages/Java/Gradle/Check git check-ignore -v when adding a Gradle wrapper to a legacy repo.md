---
title: "Check git check-ignore -v when adding a Gradle wrapper to a legacy repo"
created: 2026-06-06
type: lesson
status: seedling
source: "LEO CDP migration Phase 0c, 2026-06-06"
tags: [gradle, git, gitignore, gotcha]
---

# Check git check-ignore -v when adding a Gradle wrapper to a legacy repo

Legacy Gradle projects sometimes deliberately gitignore the wrapper (`**/gradlew`, `**/gradlew.bat`, `gradle/*`) — so 'add the wrapper' migrations silently fail at `git add`. Diagnose with `git check-ignore -v <path>`, which prints the exact .gitignore file+line that matched. Remove those rules (root .gitignore may differ from the module's) before committing the wrapper. LEO CDP had all three patterns in the monorepo-root .gitignore.

## Related

- [[Java 25 requires Gradle 9.1.0 or later]]
- [[not Gradle 9.0.0]]

Follow-up from the same migration: even after un-ignoring `gradlew`, the wrapper **jar** was still silently excluded by a module-level blanket `*.jar` rule (`core-leo-cdp/.gitignore`). Fix with a negation right after the blanket rule: `!gradle/wrapper/gradle-wrapper.jar`. Lesson: check-ignore every wrapper file individually — `gradlew`, `gradlew.bat`, `gradle-wrapper.properties`, **and** `gradle-wrapper.jar` can each be caught by a different rule in a different .gitignore.
