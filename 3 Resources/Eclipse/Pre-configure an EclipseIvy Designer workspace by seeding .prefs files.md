---
title: "Pre-configure an Eclipse/Ivy Designer workspace by seeding .prefs files"
created: 2026-07-27
type: howto
status: seedling
source: "session 2026-07-27 (Ivy setup)"
tags: [eclipse, axon-ivy, m2e, validation, workspace, howto]
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
