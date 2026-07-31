---
ai_hash: 0ab587af1b7abd9e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-07
entities: []
source: LEO CDP consolidation builds, 2026-06-07
status: seedling
tags:
- gradle
- docker
- wrapper
- network
- gotcha
title: Give the gradlew distribution download its own retried Docker layer
type: lesson
---

# Give the gradlew distribution download its own retried Docker layer

The Gradle wrapper's distribution download (services.gradle.org, ~130MB) can die mid-stream with `java.io.IOException: Premature EOF` inside Docker builds - hit twice in one day on a Rancher Desktop/WSL2 network path. Because the download shares a RUN layer with the build, every failure wastes a full build attempt. Fix: give the download its own cheap retrying layer BEFORE the build step:

```dockerfile
RUN ./gradlew --version --no-daemon || ./gradlew --version --no-daemon || ./gradlew --version --no-daemon
RUN ./gradlew AutoBuildForDeployment ...
```
`gradlew --version` forces the distribution fetch; on success the layer caches, so later builds never re-download. General pattern: any flaky network fetch in a Dockerfile deserves its own small retried layer so the expensive step never pays for it.

## Related

- [[gradlew requires xargs - minimal corretto images need findutils installed]]

Refinement 2: the retried download layer must come BEFORE `COPY . .` (copy only gradlew/gradle/ first) - otherwise every source change invalidates the cached distribution layer and you pay the flaky download on every build. Same principle as copying package manifests before source in Node/Maven images. Also raise the wrapper's own fetch timeout in gradle-wrapper.properties: `networkTimeout=60000` (default 10000ms is what fails first on slow paths).

%% ai-graph-start %%

**Related notes:**
- [[gradlew requires xargs - minimal corretto images need findutils installed]]
- [[gradlew wrapper upgrades run under the OLD Gradle version - pick the JDK accordingly]]
- [[gradlew committed from Windows loses the exec bit - fix with git update-index chmod]]
- [[Check git check-ignore -v when adding a Gradle wrapper to a legacy repo]]

%% ai-graph-end %%