---
title: "Klara/Luz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth"
created: 2026-07-27
type: lesson
status: seedling
source: "session 2026-07-27 luz_finance setup"
tags: [maven, gcp, artifact-registry, klara-luz, gotcha]
---

# Klara/Luz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth

Klara/Luz Maven modules publish and consume artifacts from **Google Artifact Registry** (`europe-west6-maven.pkg.dev/klara-repo/...`), not Maven Central or a Nexus. Two things must be in place or the build dies at dependency resolution:

1. The `com.google.cloud.artifactregistry:artifactregistry-maven-wagon` build extension in `.mvn/extensions.xml` (usually already committed).
2. Valid GCP credentials on the machine — run `gcloud auth login` **and/or** `gcloud auth application-default login` first.

Symptom when missing: `mvn` fails resolving `ch.klara.*` / `ch.xpertline.luz.*` dependencies with 401/403 or 'could not resolve'. This is environment setup, not a POM problem — don't chase the POM.

Common commands once auth works: `mvn clean install` (add `-Dmaven.test.skip=true` for fast local iteration), `mvn clean verify` (tests), `mvn clean deploy -U -B` (publish, CI only).

## Related

- [[Axon Ivy project anatomy: logic split across processes]]
- [[data classes]]
- [[HTML dialogs]]
- [[and Java]]
