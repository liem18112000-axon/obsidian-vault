---
title: "Building Klara/Luz Ivy projects off-VPN by routing Maven through Google Artifact Registry"
created: 2026-07-27
type: howto
status: seedling
source: "session 2026-07-27 (Ivy setup)"
tags: [axon-ivy, maven, gcloud, artifact-registry, vpn, klara, luz, howto]
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
