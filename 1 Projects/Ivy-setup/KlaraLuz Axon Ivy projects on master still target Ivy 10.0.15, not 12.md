---
title: "Klara/Luz Axon Ivy projects on master still target Ivy 10.0.15, not 12"
created: 2026-07-27
type: observation
status: seedling
source: "session 2026-07-27 (Ivy setup)"
tags: [axon-ivy, klara, luz, maven, version-mismatch, gotcha]
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
