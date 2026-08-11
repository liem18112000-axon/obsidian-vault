---
ai_hash: 9622fa4e9951a21c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities:
- KlaraLuz Axon Ivy projects
- master branch
- Ivy 10.0.15
- Ivy 12
- '2026-07-27'
- axonivy-prod
- Bitbucket
- Ivy Designer 12.0.16
- klara_prototype/pom.xml
- ivyVersion
- ivy.engine.version
- iar packaging
- ch.ivyteam.ivy:project-build-plugin
- luz_components/pom.xml
- ch.klara.ivy:luz_ivy_common:2.0.01.0
- klara_theme/pom.xml
- jar packaging
- Maven
- CLI Maven builds
- Ivy 10.0.15 cache
- ~/.m2/settings.xml
- ch profile
- migration prompt
- Ivy Designer 10.0.16
- guide
- luz_ivy_common
- klara_faces
- luz_templates
- luz_common
- xent_*
- incamail
- ch.ivyteam.ivy.addons
- corporate registry
- Google Artifact Registry
- europe-west6-maven.pkg.dev/klara-repo/...
- repo.axongroupio.ch/artifactory
- EclipseIvy Designer workspace
- .prefs files
source: session 2026-07-27 (Ivy setup)
status: seedling
tags:
- axon-ivy
- klara
- luz
- maven
- version-mismatch
- gotcha
title: Klara/Luz Axon Ivy projects on master still target Ivy 10.0.15, not 12
type: observation
---

# Klara/Luz Axon Ivy projects on master still target Ivy 10.0.15, not 12

As of 2026-07-27, the `master` branches of the Klara/Luz Axon Ivy repos (`axonivy-prod` on Bitbucket) still build against **Ivy 10.0.15**, despite a freshly downloaded **Ivy Designer 12.0.16**. Do not assume "master == latest Ivy".

Evidence:
- `klara_prototype/pom.xml`: `<ivyVersion>10.0.15</ivyVersion>`, `<ivy.engine.version>10.0.15</ivy.engine.version>`, packaging `iar`, uses `ch.ivyteam.ivy:project-build-plugin:${ivyVersion}`.
- `luz_components/pom.xml`: packaging `iar`, parent `ch.klara.ivy:luz_ivy_common:2.0.01.0` (Ivy version defined in that uncloned parent).
- `klara_theme/pom.xml`: packaging **`jar`** (plain PrimeFaces/JSF theme lib) — not Ivy-versioned, builds with plain Maven.

Implications:
- **CLI Maven builds** are fine on Ivy 10.0.15: the pom pins `project-build-plugin` 10.0.15 and the engine 10.0.15 is already cached at `~/.m2/repository/.cache/ivy/10.0.15`; the existing `~/.m2/settings.xml` `ch` profile already matches (no rewiring needed).
- **Ivy Designer 12.0.16 mismatches** these Ivy-10 projects — opening/running them in it triggers a migration prompt; to develop in-IDE you want Designer **10.0.16** (the guide's version).
- **Dependency graph exceeds the 3 cloned repos**: builds also need `luz_ivy_common` (parent), `klara_faces`, `luz_templates`, `luz_common`, `xent_*` (6+ modules), `incamail`, `ch.ivyteam.ivy.addons` — resolved from the corporate registry. Projects deploy to Google Artifact Registry (`europe-west6-maven.pkg.dev/klara-repo/...`); dependency resolution repos are set to `repo.axongroupio.ch/artifactory` in settings.xml.

## Related
[[3 Resources/Tooling/Eclipse/Pre-configure an EclipseIvy Designer workspace by seeding .prefs files]]

## Related

- [[3 Resources/Tooling/Eclipse/Pre-configure an EclipseIvy Designer workspace by seeding .prefs files]]

%% ai-graph-start %%

**Related notes:**
- [[Building KlaraLuz Ivy projects off-VPN by routing Maven through Google Artifact Registry]]
- [[KlaraLuz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth]]
- [[Axon Ivy project anatomy logic split across processes, data classes, HTML dialogs, and Java]]
- [[Axon Ivy installEngine fails when ivy.engine.directory is stale or unwritable]]
- [[luz_finance and luz_components move in lockstep SNAPSHOTs; a 'method not applicable' compile error usually means a skew]]

**Relations:**
- KlaraLuz Axon Ivy projects — *are on* — master branch
- master branch — *targets* — Ivy 10.0.15
- master branch — *does not target* — Ivy 12
- KlaraLuz Axon Ivy projects — *are in repo* — axonivy-prod
- axonivy-prod — *is hosted on* — Bitbucket
- KlaraLuz Axon Ivy projects — *status as of* — 2026-07-27
- Ivy Designer 12.0.16 — *is a version of* — Ivy Designer
- klara_prototype/pom.xml — *sets property* — ivyVersion
- ivyVersion — *value is* — 10.0.15
- klara_prototype/pom.xml — *sets property* — ivy.engine.version
- ivy.engine.version — *value is* — 10.0.15
- klara_prototype/pom.xml — *uses packaging* — iar packaging
- klara_prototype/pom.xml — *uses plugin* — ch.ivyteam.ivy:project-build-plugin
- luz_components/pom.xml — *uses packaging* — iar packaging
- luz_components/pom.xml — *has parent* — ch.klara.ivy:luz_ivy_common:2.0.01.0
- ch.klara.ivy:luz_ivy_common:2.0.01.0 — *defines* — Ivy version
- klara_theme/pom.xml — *uses packaging* — jar packaging
- klara_theme/pom.xml — *builds with* — Maven
- CLI Maven builds — *are compatible with* — Ivy 10.0.15
- CLI Maven builds — *pin plugin* — ch.ivyteam.ivy:project-build-plugin 10.0.15
- CLI Maven builds — *pin engine* — Ivy 10.0.15
- Ivy 10.0.15 — *is cached at* — Ivy 10.0.15 cache
- ~/.m2/settings.xml — *contains profile* — ch profile
- Ivy Designer 12.0.16 — *mismatches* — Ivy 10.0.15 projects
- opening Ivy 10.0.15 projects — *in* — Ivy Designer 12.0.16
- opening Ivy 10.0.15 projects — *triggers* — migration prompt
- Ivy Designer 10.0.16 — *is recommended for* — in-IDE development
- Ivy Designer 10.0.16 — *is* — guide's version
- KlaraLuz Axon Ivy projects — *depend on* — luz_ivy_common
- KlaraLuz Axon Ivy projects — *depend on* — klara_faces
- KlaraLuz Axon Ivy projects — *depend on* — luz_templates
- KlaraLuz Axon Ivy projects — *depend on* — luz_common
- KlaraLuz Axon Ivy projects — *depend on* — xent_*
- KlaraLuz Axon Ivy projects — *depend on* — incamail
- KlaraLuz Axon Ivy projects — *depend on* — ch.ivyteam.ivy.addons
- dependencies — *are resolved from* — corporate registry
- KlaraLuz Axon Ivy projects — *deploy to* — Google Artifact Registry
- Google Artifact Registry — *is located at* — europe-west6-maven.pkg.dev/klara-repo/...
- dependency resolution repos — *are configured in* — ~/.m2/settings.xml
- dependency resolution repos — *include* — repo.axongroupio.ch/artifactory
- EclipseIvy Designer workspace — *can be pre-configured with* — .prefs files

%% ai-graph-end %%