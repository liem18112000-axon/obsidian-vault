---
ai_hash: 5d01925cc4b966c2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration Phase 0c, 2026-06-06
status: seedling
tags:
- gradle
- git
- gitignore
- gotcha
title: Check git check-ignore -v when adding a Gradle wrapper to a legacy repo
type: lesson
---

# Check git check-ignore -v when adding a Gradle wrapper to a legacy repo

Legacy Gradle projects sometimes deliberately gitignore the wrapper (`**/gradlew`, `**/gradlew.bat`, `gradle/*`) — so 'add the wrapper' migrations silently fail at `git add`. Diagnose with `git check-ignore -v <path>`, which prints the exact .gitignore file+line that matched. Remove those rules (root .gitignore may differ from the module's) before committing the wrapper. LEO CDP had all three patterns in the monorepo-root .gitignore.

## Related

- [[3 Resources/Languages/Java/Gradle/Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]

Follow-up from the same migration: even after un-ignoring `gradlew`, the wrapper **jar** was still silently excluded by a module-level blanket `*.jar` rule (`core-leo-cdp/.gitignore`). Fix with a negation right after the blanket rule: `!gradle/wrapper/gradle-wrapper.jar`. Lesson: check-ignore every wrapper file individually — `gradlew`, `gradlew.bat`, `gradle-wrapper.properties`, **and** `gradle-wrapper.jar` can each be caught by a different rule in a different .gitignore.

%% ai-graph-start %%

**Related notes:**
- [[gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly]]
- [[gradlew committed from Windows loses the exec bit - fix with git update-index chmod]]
- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]
- [[Wall of NoClassDefFoundError on first test run = static-init IO, split unit from integration]]
- [[String-typed org.gradle.jvm.environment attribute collides with Gradle 7+ typed TargetJvmEnvironment]]

%% ai-graph-end %%