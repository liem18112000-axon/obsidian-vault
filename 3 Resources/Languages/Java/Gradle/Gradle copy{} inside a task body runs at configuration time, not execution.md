---
ai_hash: 69d756b720b9b234
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
title: Gradle copy{} inside a task body runs at configuration time, not execution
type: gotcha
---

# Gradle copy{} inside a task body runs at configuration time, not execution

Calling the `copy { ... }` *method* (project.copy) inside a task's configuration body — instead of declaring a `Copy`-typed task or putting it in `doLast` — executes the copy during the **configuration phase**, on *every* Gradle invocation, regardless of which task was requested.

Confirmed empirically in LEO CDP: `task CopyDevOpsScriptToBUILD { copy {...} }` had populated `build/dist-release/devops-script` while the requested build task was still resolving dependencies — before compilation even began.

Symptoms: mysterious files appearing even for `gradle tasks`/unrelated targets; slow configuration; configuration-cache incompatibility.

Fix: make it a real `Copy` task (`tasks.register('X', Copy) { from...; into... }`) or wrap in `doLast`. Spot it in reviews: `copy {`, `delete(`, `exec {` directly inside a task body are config-time calls.

%% ai-graph-start %%

**Related notes:**
- [[Check git check-ignore -v when adding a Gradle wrapper to a legacy repo]]
- [[Baseline-diff gates must compare post-build to post-build when artifacts are committed]]

%% ai-graph-end %%