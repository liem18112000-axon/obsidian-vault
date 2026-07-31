---
title: "Axon Ivy project anatomy: logic split across processes, data classes, HTML dialogs, and Java"
created: 2026-07-27
type: concept
status: seedling
source: "session 2026-07-27 luz_finance setup"
tags: [axon-ivy, klara-luz, project-structure, java]
---

# Axon Ivy project anatomy: logic split across processes, data classes, HTML dialogs, and Java

An Axon Ivy project is not just Java — application logic is deliberately split across several parallel trees, all compiled together into one `iar` (Ivy Archive). Knowing the split is essential to navigating any Klara/Luz Ivy module (luz_finance, luz_components, etc.).

- **`processes/`** — visual process models (`.p.json`): the workflow/orchestration and entry points. The domain folder names here are the fastest map of the app's feature surface.
- **`dataclasses/`** — data-class definitions (`.ivyClass`): the domain/entity/process data model.
- **`src_dataClasses/`** — **generated** Java produced from `dataclasses/` (git-ignored). Gotcha: never hand-edit; change the `.ivyClass` and let the build regenerate.
- **`src_hd/`** — HTML Dialogs: reusable UI components, each a PrimeFaces/JSF `.xhtml` paired with `rddescriptor`/`.json`/`.ivyClass` metadata, `.scss`, and backing `.java`.
- **`src/` + `src_test/`** — plain Java business logic and JUnit tests.
- **`cms/`** — Content Management System: i18n labels/text/styles.
- **`config/`** — runtime config (variables.yaml, databases.yaml, rest-clients.yaml, roles.xml, users.xml, ...).

`.p.json`, `.ivyClass`, and HTML-dialog descriptors are Ivy-managed: JSON/XML text you *can* read/edit directly, but normally authored via the Axon Ivy Designer, so hand-edits must preserve the (ID/position-sensitive) structure. The project builds to a library `iar`, not a standalone app — there is no `mvn run`; you run it inside an Axon Ivy Engine / Designer.

## Related

- [[Klara/Luz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth]]
