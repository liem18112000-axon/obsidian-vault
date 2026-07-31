---
ai_hash: 56edae88e7e7c410
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities: []
source: session 2026-07-27 luz_finance setup
status: seedling
tags:
- maven
- gcp
- artifact-registry
- klara-luz
- gotcha
title: Klara/Luz Maven builds resolve dependencies from Google Artifact Registry and
  require gcloud auth
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[Building KlaraLuz Ivy projects off-VPN by routing Maven through Google Artifact Registry]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]
- [[klara-nonprod is the GCP project for non-prod Artifact Registry IAM]]
- [[luz-docs Cloud Build pushes an image for every branch but only master updates luz_kubernetes]]

%% ai-graph-end %%