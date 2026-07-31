---
title: "Curate OpenRewrite UpgradeToJava25 - composite includes instance-main and wrapper bumps"
created: 2026-06-07
type: lesson
status: seedling
source: "LEO CDP Wave 1 dry-run review, 2026-06-07"
tags: [openrewrite, java25, migration, gotcha, recipes]
---

# Curate OpenRewrite UpgradeToJava25 - composite includes instance-main and wrapper bumps

OpenRewrite's `UpgradeToJava25` composite recipe is NOT all mechanical safety - on LEO CDP it wanted 267 file changes, dominated by recipes you likely must reject for a production codebase: **MigrateMainMethodToInstanceMain** (114 hits - rewrites `public static void main` to JEP-512 instance main; breaks reflective `Clazz.main(args)` callers and buys nothing for deployed services), **ReplaceSystemOutWithIOPrint** (175 hits - println -> java.lang.IO churn), and **UpdateGradleWrapper/UpgradePluginsForJava** (silently bumps your deliberately-pinned wrapper). Curate instead: activate the specific value recipes - SequencedCollection/ListFirstAndLast, ReplaceUnusedVariablesWithUnderscore, UseTextBlocks, PathsGetToPathOf, staticanalysis.InstanceOfPatternMatch, RemoveExtraSemicolons, AddSerialAnnotationToSerialVersionUID. Rule: always `rewriteDryRun` first and read the per-recipe frequency table (grep the log for org.openrewrite recipe names | sort | uniq -c) before any `rewriteRun`.

## Related

- [[Run OpenRewrite via Gradle init script without touching build.gradle]]
