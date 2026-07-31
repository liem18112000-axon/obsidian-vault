---
title: "Run OpenRewrite via Gradle init script without touching build.gradle"
created: 2026-06-07
type: howto
status: seedling
source: "LEO CDP Wave 1, 2026-06-07"
tags: [openrewrite, gradle, init-script, migration, technique]
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

- [[Java 25 requires Gradle 9.1.0 or later]]
- [[not Gradle 9.0.0]]
