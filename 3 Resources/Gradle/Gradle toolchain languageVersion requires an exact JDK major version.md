---
title: "Gradle toolchain languageVersion requires an exact JDK major version"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24 ecomart-java analysis"
tags: [gradle, java, toolchain, gotcha, build]
---

# Gradle toolchain languageVersion requires an exact JDK major version

A Gradle `java { toolchain { languageVersion.set(JavaLanguageVersion.of(18)) } }` block requires an **exactly-matching** JDK (major version 18) to be installed, or a toolchain download repository configured — it does **not** mean "18 or higher".

If only other JDKs are present (e.g. 17, 19, 25), the build fails at *toolchain resolution*, before compiling anything:

```
No matching toolchains found for requested specification:
{languageVersion=18, vendor=any, implementation=vendor-specific}
No locally installed toolchains match and toolchain download repositories have not been configured.
```

**Fixes:** install the exact JDK; add the foojay `org.gradle.toolchains.foojay-resolver-convention` plugin so Gradle auto-downloads the right JDK; or relax the pinned version.

**Gotcha to watch:** version drift between the README ("Java 18 or higher"), the toolchain pin (exactly 18), and CI `actions/setup-java` (which may install a *different* version like 17). All three should agree, or the build is not reproducible.

## Related

- [[Verify gradle-wrapper.jar integrity before running gradlew]]
