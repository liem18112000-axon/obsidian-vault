---
ai_hash: 2af3e15fb74c1ed0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities:
- Klara/Luz Axon Ivy projects
- Maven
- Google Artifact Registry
- VPN
- axonivy-prod Bitbucket
- internal Maven artifacts
- repo.axongroupio.ch
- JFrog Artifactory
- 10.124.0.59
- quarkus profile
- ch profile
- ~/.m2/settings.xml
- europe-west6-maven.pkg.dev/klara-repo/...
- <repositories>
- project poms
- com.google.cloud.artifactregistry:artifactregistry-maven-wagon extension
- .mvn/extensions.xml
- gcloud ADC
- public internet
- Maven Central
- Ivy ch properties
- ivyVersion=10.0.15
- engine dir ~/.m2/repository/.cache/ivy/10.0.15
- axongroupio repositories
- axongroupio pluginRepositories
- gcloud auth application-default print-access-token
- klara_theme
- luz_components
- ch.klara.ivy:luz_ivy_common:2.0.01.0
- snapshot retention
- internal SNAPSHOTs
- klara_prototype master
- klara_theme:1.00.22.00-SNAPSHOT
- klara_theme:1.00.48.00-SNAPSHOT
- corporate VPN
- Ivy 10.0.15
- Ivy 12
- Bitbucket repo
- app password
- inline credential helper
source: session 2026-07-27 (Ivy setup)
status: seedling
tags:
- axon-ivy
- maven
- gcloud
- artifact-registry
- vpn
- klara
- luz
- howto
title: Building Klara/Luz Ivy projects off-VPN by routing Maven through Google Artifact
  Registry
type: howto
---

# Building Klara/Luz Ivy projects off-VPN by routing Maven through Google Artifact Registry

The Klara/Luz Axon Ivy projects (`axonivy-prod` Bitbucket) resolve internal Maven artifacts from **two** places, and off the corporate VPN only one works:

- `repo.axongroupio.ch` (JFrog Artifactory) — private IP `10.124.0.59`, **VPN-only**; off-VPN every request times out (~6s each) and that timeout is what fails the build. It is injected by the `quarkus`/`ch` profiles in `~/.m2/settings.xml`.
- **Google Artifact Registry** `europe-west6-maven.pkg.dev/klara-repo/...` — declared as `<repositories>` in the project poms, using the `com.google.cloud.artifactregistry:artifactregistry-maven-wagon` extension (`.mvn/extensions.xml`) + **gcloud ADC**. Reachable over the public internet.

**Recipe to build off-VPN:** run Maven with a settings.xml that KEEPS Maven Central + the Ivy `ch` properties (`ivyVersion=10.0.15`, engine dir `~/.m2/repository/.cache/ivy/10.0.15`) but DROPS the axongroupio repositories/pluginRepositories, and ensure `gcloud auth application-default print-access-token` works. Add `-U` so snapshot metadata refreshes from GAR instead of the dead host. Verified: `klara_theme` (plain `jar`) builds SUCCESS; `luz_components` `validate` SUCCESS (release parent `ch.klara.ivy:luz_ivy_common:2.0.01.0` resolves from GAR).

**Residual blocker (not infra):** GAR snapshot **retention is short**, so old pinned internal SNAPSHOTs are purged. `klara_prototype` master pins `klara_theme:1.00.22.00-SNAPSHOT` (timestamp `20250725.044911-3`) which no longer exists in GAR (current master theme is `1.00.48.00-SNAPSHOT`) → `Could not find artifact`. Fixing that needs either the corporate VPN (Artifactory retains more snapshots), bumping the pin to a current version (source change, API-drift risk), or building the matching klara_theme commit locally. See [[1 Projects/Ivy-setup/KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]].

## Related
[[1 Projects/Ivy-setup/KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]
[[Clone a Bitbucket repo with an app password without leaking it (inline credential helper)]]

## Related

- [[Klara/Luz Axon Ivy projects on master still target Ivy 10.0.15]]
- [[not 12]]
- [[Clone a Bitbucket repo with an app password without leaking it (inline credential helper)]]

%% ai-graph-start %%

**Related notes:**
- [[KlaraLuz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth]]
- [[KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]
- [[Klara Cloud Build pushes images to klara-repo Artifact Registry with the SA on the trigger]]
- [[Vinnstack Cloud Build trigger lives in klara-infra, not klara-nonprod]]
- [[klara-nonprod is the GCP project for non-prod Artifact Registry IAM]]

**Relations:**
- Klara/Luz Axon Ivy projects — *are built off-VPN by routing* — Maven
- Maven — *through* — Google Artifact Registry
- Klara/Luz Axon Ivy projects — *are stored in* — axonivy-prod Bitbucket
- Klara/Luz Axon Ivy projects — *resolve* — internal Maven artifacts
- internal Maven artifacts — *are resolved from* — repo.axongroupio.ch
- internal Maven artifacts — *are resolved from* — Google Artifact Registry
- repo.axongroupio.ch — *is a type of* — JFrog Artifactory
- repo.axongroupio.ch — *has private IP* — 10.124.0.59
- repo.axongroupio.ch — *is* — VPN-only
- Google Artifact Registry — *is located at* — europe-west6-maven.pkg.dev/klara-repo/...
- Google Artifact Registry — *is reachable over* — public internet
- Google Artifact Registry — *uses* — com.google.cloud.artifactregistry:artifactregistry-maven-wagon extension
- Google Artifact Registry — *uses* — gcloud ADC
- Maven — *is configured by* — ~/.m2/settings.xml
- ~/.m2/settings.xml — *injects* — quarkus profile
- ~/.m2/settings.xml — *injects* — ch profile
- com.google.cloud.artifactregistry:artifactregistry-maven-wagon extension — *is declared in* — .mvn/extensions.xml
- Maven — *uses* — project poms
- project poms — *declare* — <repositories>
- Maven — *can be run with settings.xml that drops* — axongroupio repositories
- Maven — *can be run with settings.xml that drops* — axongroupio pluginRepositories
- Maven — *keeps* — Maven Central
- Maven — *keeps* — Ivy ch properties
- Ivy ch properties — *include* — ivyVersion=10.0.15
- Ivy ch properties — *include* — engine dir ~/.m2/repository/.cache/ivy/10.0.15
- gcloud ADC — *can be checked with* — gcloud auth application-default print-access-token
- klara_theme — *is a* — plain jar
- luz_components — *has parent* — ch.klara.ivy:luz_ivy_common:2.0.01.0
- ch.klara.ivy:luz_ivy_common:2.0.01.0 — *resolves from* — Google Artifact Registry
- Google Artifact Registry — *has* — snapshot retention
- snapshot retention — *is* — short
- snapshot retention — *purges* — old pinned internal SNAPSHOTs
- klara_prototype master — *pins* — klara_theme:1.00.22.00-SNAPSHOT
- klara_theme:1.00.22.00-SNAPSHOT — *no longer exists in* — Google Artifact Registry
- klara_theme:1.00.48.00-SNAPSHOT — *is current master theme for* — klara_theme
- JFrog Artifactory — *retains* — more snapshots
- Klara/Luz Axon Ivy projects — *target* — Ivy 10.0.15
- Klara/Luz Axon Ivy projects — *do not target* — Ivy 12
- Bitbucket repo — *can be cloned with* — app password
- Bitbucket repo — *can be cloned with* — inline credential helper
- VPN — *is a type of* — corporate VPN

%% ai-graph-end %%