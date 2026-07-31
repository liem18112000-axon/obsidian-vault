---
ai_hash: 26b414f7de1f5014
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities: []
source: session 2026-07-27 luz_finance setup
status: seedling
tags:
- maven
- settings
- profiles
- technique
title: Maven user settings.xml overrides global same-id profile properties
type: lesson
---

# Maven user settings.xml overrides global same-id profile properties

Maven merges the **global** settings (`$M2_HOME/conf/settings.xml`) with the **user** settings (`~/.m2/settings.xml`), and the user file wins on conflicts. This includes `<properties>` inside a `<profile>`: define a profile with the **same `<id>`** in your user settings, set the property to the value you want, and activate it — the user value overrides the global profile's value in the effective settings.

**Why this is useful:** it lets you correct a broken/stale value in a *shared* global settings.xml (e.g. an engine path pointing at another user's home) **without editing the shared file** — handy when the global file is not writable or is owned by the machine setup. 

**Verify before relying on it:** `mvn help:effective-settings` prints the merged result; grep it for your property to confirm the override took effect. Remember to also add the profile id under `<activeProfiles>` in the user settings (activation does not carry over automatically for a newly-added id).

## Related

- [[Axon Ivy installEngine fails when ivy.engine.directory is stale or unwritable]]

%% ai-graph-start %%

**Related notes:**
- [[Axon Ivy installEngine fails when ivy.engine.directory is stale or unwritable]]
- [[Pre-configure an EclipseIvy Designer workspace by seeding .prefs files]]
- [[KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]

%% ai-graph-end %%