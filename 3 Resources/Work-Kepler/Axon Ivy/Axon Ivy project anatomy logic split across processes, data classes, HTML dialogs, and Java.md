---
ai_hash: c5855e193c00c099
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities: []
source: session 2026-07-27 luz_finance setup
status: seedling
tags:
- axon-ivy
- klara-luz
- project-structure
- java
title: 'Axon Ivy project anatomy: logic split across processes, data classes, HTML
  dialogs, and Java'
type: concept
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

- [[3 Resources/Work-Kepler/Klara/KlaraLuz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth]]

%% ai-graph-start %%

**Related notes:**
- [[KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]
- [[3 Resources]]
- [[luz_epost_business_web to luz_docs_view_controller integration goes through one REST client package]]
- [[KlaraLuz Maven builds resolve dependencies from Google Artifact Registry and require gcloud auth]]
- [[luz_finance and luz_components move in lockstep SNAPSHOTs; a 'method not applicable' compile error usually means a skew]]

%% ai-graph-end %%