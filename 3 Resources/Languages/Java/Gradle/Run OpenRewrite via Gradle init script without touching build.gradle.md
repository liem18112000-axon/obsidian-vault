---
ai_hash: c8cc42d6d37bb4a2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities: []
source: LEO CDP Wave 1, 2026-06-07
status: seedling
tags:
- openrewrite
- gradle
- init-script
- migration
- technique
title: Run OpenRewrite via Gradle init script without touching build.gradle
type: howto
---

# Run OpenRewrite via Gradle init script without touching build.gradle

To run OpenRewrite on a project whose build script you don't want to modify (e.g. mid-migration, or a build you don't own), apply the plugin through an **init script** instead of build.gradle:

```groovy
// rewrite-init.gradle
initscript {
  repositories { mavenCentral(); gradlePluginPortal() }
  dependencies { classpath("org.openrewrite:plugin:latest.release") }
}
rootProject {
  plugins.apply(org.openrewrite.gradle.RewritePlugin)
  dependencies { rewrite("org.openrewrite.recipe:rewrite-migrate-java:latest.release") }
  rewrite { activeRecipe("org.openrewrite.java.migrate.UpgradeToJava25") }
}
```
Then: `./gradlew --init-script rewrite-init.gradle rewriteDryRun` -> reviewable patch at `build/reports/rewrite/rewrite.patch` (no files modified); `rewriteRun` applies. Keep the init script out of version control. Always dry-run first and apply per-package in reviewable chunks.

## Related

- [[3 Resources/Languages/Java/Gradle/Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]

%% ai-graph-start %%

**Related notes:**
- [[Curate OpenRewrite UpgradeToJava25 - composite includes instance-main and wrapper bumps]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Java 25 requires Gradle 9.1.0 or later, not Gradle 9.0.0]]
- [[gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly]]
- [[Check git check-ignore -v when adding a Gradle wrapper to a legacy repo]]

%% ai-graph-end %%