---
title: "Verify gradle-wrapper.jar integrity before running gradlew"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24 ecomart-java analysis"
tags: [gradle, security, supply-chain, wrapper]
---

# Verify gradle-wrapper.jar integrity before running gradlew

`gradle-wrapper.jar` runs with **your** privileges the first time anyone executes `./gradlew`, so a tampered wrapper jar is a classic supply-chain foothold. Verify it before trusting an unfamiliar repo.

- Confirm the size/checksum against a known-good release. The official **Gradle 8.3** wrapper jar is **61,608 bytes**, SHA-256 `91941f522fbfd4431cf57e445fc3d5200c85f957bda2de5251353cf11174f4b5`.
- `sha256sum gradle/wrapper/gradle-wrapper.jar` and compare.
- Check `gradle/wrapper/gradle-wrapper.properties` → `distributionUrl` points at the official `https://services.gradle.org` over HTTPS.

**Hardening:** add a `distributionSha256Sum=<hash>` line to `gradle-wrapper.properties` so the wrapper refuses to run a distribution whose hash doesn't match. GitHub's `gradle/wrapper-validation-action` automates this check in CI.

Note: a `gradlew`/`gradlew.bat` showing as "modified" in git is often just CRLF↔LF line-ending churn — diff the content before assuming an injected payload.

## Related

- [[Gradle toolchain languageVersion requires an exact JDK major version]]
