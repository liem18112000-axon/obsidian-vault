---
ai_hash: bdcbc590e97a4ca0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities: []
source: session 2026-07-27 (Ivy setup)
status: seedling
tags:
- eclipse
- axon-ivy
- m2e
- validation
- workspace
- howto
title: Pre-configure an Eclipse/Ivy Designer workspace by seeding .prefs files
type: howto
---

# Pre-configure an Eclipse/Ivy Designer workspace by seeding .prefs files

Eclipse reads existing preference files under `<workspace>/.metadata/.plugins/org.eclipse.core.runtime/.settings/` the first time it opens a workspace, so you can **pre-apply workspace settings on disk without launching the GUI** — useful when a GUI launch is blocked/declined or you want a reproducible setup. Axon Ivy Designer is Eclipse-based, so this applies to it too.

**Reliably seedable via `.prefs` (Java properties format):**

- **Disable all validation** — `org.eclipse.wst.validation.prefs`:
  ```
  eclipse.preferences.version=1
  override=true
  suspend=true
  vf.version=3
  ```
  `suspend=true` == the "Suspend all validators" checkbox (Preferences > Validation).
- **Point m2e/Maven at user settings** — `org.eclipse.m2e.core.prefs`:
  ```
  eclipse.preferences.version=1
  eclipse.m2.userSettingsFile=C\:\Users\me\.m2\settings.xml
  ```
  Backslashes must be doubled in the properties value.

**Not reliably seedable pre-launch:** editor/file associations (e.g. making *HTML Editor* the default for `*.xhtml`) live in `workbench.xml`, which Eclipse only generates on first launch — leave that as a one-time GUI step (General > Editors > File Associations).

To use a seeded workspace, launch the app and select that folder at the workspace prompt.

## Related
[[Resources index]]

## Related

- [[Resources index]]

%% ai-graph-start %%

**Related notes:**
- [[Maven user settings.xml overrides global same-id profile properties]]
- [[KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]
- [[Axon Ivy installEngine fails when ivy.engine.directory is stale or unwritable]]

%% ai-graph-end %%