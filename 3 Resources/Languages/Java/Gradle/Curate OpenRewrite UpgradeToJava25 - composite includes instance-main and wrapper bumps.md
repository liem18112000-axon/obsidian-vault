---
ai_hash: 081fafde691bad9c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities: []
source: LEO CDP Wave 1 dry-run review, 2026-06-07
status: seedling
tags:
- openrewrite
- java25
- migration
- gotcha
- recipes
title: Curate OpenRewrite UpgradeToJava25 - composite includes instance-main and wrapper
  bumps
type: lesson
---

# Curate OpenRewrite UpgradeToJava25 - composite includes instance-main and wrapper bumps

OpenRewrite's `UpgradeToJava25` composite recipe is NOT all mechanical safety - on LEO CDP it wanted 267 file changes, dominated by recipes you likely must reject for a production codebase: **MigrateMainMethodToInstanceMain** (114 hits - rewrites `public static void main` to JEP-512 instance main; breaks reflective `Clazz.main(args)` callers and buys nothing for deployed services), **ReplaceSystemOutWithIOPrint** (175 hits - println -> java.lang.IO churn), and **UpdateGradleWrapper/UpgradePluginsForJava** (silently bumps your deliberately-pinned wrapper). Curate instead: activate the specific value recipes - SequencedCollection/ListFirstAndLast, ReplaceUnusedVariablesWithUnderscore, UseTextBlocks, PathsGetToPathOf, staticanalysis.InstanceOfPatternMatch, RemoveExtraSemicolons, AddSerialAnnotationToSerialVersionUID. Rule: always `rewriteDryRun` first and read the per-recipe frequency table (grep the log for org.openrewrite recipe names | sort | uniq -c) before any `rewriteRun`.

## Related

- [[Run OpenRewrite via Gradle init script without touching build.gradle]]

%% ai-graph-start %%

**Related notes:**
- [[Run OpenRewrite via Gradle init script without touching build.gradle]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Raising bytecode target to 25 on same JVM swept k6 rounds - free-to-positive]]
- [[Verify wildcard-to-explicit import cleanup by compiling]]
- [[gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly]]

%% ai-graph-end %%