---
ai_hash: 462d80c2aaf62e06
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities: []
source: session 2026-07-27 luz_finance setup
status: seedling
tags:
- axon-ivy
- maven
- build
- gotcha
- klara-luz
title: Axon Ivy installEngine fails when ivy.engine.directory is stale or unwritable
type: lesson
---

# Axon Ivy installEngine fails when ivy.engine.directory is stale or unwritable

The Axon Ivy `com.axonivy.ivy.ci:project-build-plugin` binds an **installEngine** goal early in the build that locates (or downloads + unpacks) a full Axon Ivy Engine of the required version. It resolves the engine location from the `ivy.engine.directory` Maven property (with `engineDirectory`/`engineDirectoryCache`/`ivy-server-path` as related keys); the default cache is `${localRepository}/.cache/ivy/<version>`.

**Gotcha:** if that property points at a directory the current OS user cannot write to — classically a path baked into the machine's *global* `settings.xml` referencing a **previous user's** home (e.g. `C:\Users\<someone-else>\.m2\repository\.cache\ivy\...`) — installEngine fails with `Failed to unpack downloaded engine ... Cannot create output directories`. It is not a network/download problem.

**Fix:** point `ivy.engine.directory` (and the sibling engine keys) at a writable, already-unpacked engine of the *matching* version, and make sure the plugin version equals the engine version (a stale `ivyVersion` in settings.xml can force an older plugin against a newer engine). A healthy run logs `Using engine in '<dir>'` with no download. See [[Maven user settings.xml overrides global same-id profile properties]] for a non-invasive way to override the bad global value, and [[3 Resources/Work-Kepler/Axon Ivy/Axon Ivy project anatomy logic split across processes, data classes, HTML dialogs, and Java]].

## Related

- [[Maven user settings.xml overrides global same-id profile properties]]
- [[Axon Ivy project anatomy: logic split across processes]]
- [[data classes]]
- [[HTML dialogs]]
- [[and Java]]

%% ai-graph-start %%

**Related notes:**
- [[Maven user settings.xml overrides global same-id profile properties]]
- [[KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]
- [[Building KlaraLuz Ivy projects off-VPN by routing Maven through Google Artifact Registry]]
- [[Pre-configure an EclipseIvy Designer workspace by seeding .prefs files]]

%% ai-graph-end %%