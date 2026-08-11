---
ai_hash: d8d887b4771ed1ff
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: evergreen
tags:
- behave
- integration-test
- debugging
- regression
- flakiness
- technique
title: Isolate the same scenarios on both branches to separate regression from flakiness
type: howto
---

# Isolate the same scenarios on both branches to separate regression from flakiness

To tell a real branch-introduced regression apart from environmental/timing flakiness in a behave (or any) suite, run the **same scenarios in isolation on BOTH branches under identical conditions** (same runner mode, same tenant, same machine). 

The trap: comparing *branch-in-isolation* against *master-from-a-full-suite-run* is not apples-to-apples. A full run warms caches and gives read-after-write/eventual-consistency time to settle, so timing-sensitive scenarios that fail in isolation often pass in the full run. That asymmetry masquerades as "the branch broke it."

Decision rule once both are isolated:
- byte-identical scenario passes on master-isolation but fails on branch-isolation -> **branch regression** (look at what the branch changed in the consuming code path).
- fails on BOTH in isolation but passes in full runs -> **timing/flakiness** (or a pre-existing issue), not the branch.

Applied 2026-06-16 to luz-docs IT: running master`s `update_patch_folder` trio in isolation (3/3 pass) vs the branch trio in isolation (3/3 fail) — on a scenario whose feature text was byte-identical — proved the failure came from a branch change to a step helper, not from the feature or from timing.

Related: [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]

## Related

- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]

%% ai-graph-start %%

**Related notes:**
- [[behave step patterns differing only by quote style are distinct definitions]]
- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]
- [[dev-staging luz-docs IT failures cluster on the materialize read-path]]
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]

%% ai-graph-end %%