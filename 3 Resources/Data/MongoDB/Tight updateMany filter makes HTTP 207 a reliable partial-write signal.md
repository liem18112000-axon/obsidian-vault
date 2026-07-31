---
title: "Tight updateMany filter makes HTTP 207 a reliable partial-write signal"
created: 2026-06-09
type: lesson
status: seedling
source: "luz_docs materialize code review, 2026-06-08 (finding #25) / 2026-06-09"
tags: [mongodb, jsonstore, idempotency, multi-status, cascade, correctness, design, gotcha, luz-docs]
---

# Tight updateMany filter makes HTTP 207 a reliable partial-write signal

`matchedCount > modifiedCount` (surfaced by luz_jsonstore as HTTP **207 SC_MULTI_STATUS**) has **two indistinguishable causes**: (a) matched docs already held the target value — correctly unchanged; (b) matched docs should have changed but the write did not apply — a genuine partial failure leaving stale data. Counts alone cannot separate them. Whether 207 is benign is decided by **filter tightness, not the status code**.

- **Tight filter** — matches only docs that still NEED the change; encode the "stale" predicate in the filter, e.g. a `$expr` comparing stored value to the freshly-computed target (`$setEquals` / `$ne`). Then every matched doc MUST be modified, so `matched > modified` is unambiguously a real partial failure → retry / mark PARTIAL / sweep. A converged run matches 0 docs → clean 200.
- **Loose filter (anti-pattern)** — matches a superset (`folderIds $in affected`). Idempotent re-stamps make `modified < matched` the normal case, so 207 is noise. Swallowing it silently drops stragglers; retrying loops forever (deterministic re-run reproduces it); rolling back reverts correct docs. Neither blanket policy is right — either tighten the filter, or judge convergence by re-querying the still-stale set (`count(stale) == 0` ⇒ converged).

**Caveat:** the matched==modified invariant only covers the field(s) the FILTER inspects. Derived fields the pipeline writes but the filter ignores can still drift; 207 says nothing about them.

**luz_docs instance — two sibling cascades, opposite tightness:** `buildFolderRenameFilter` is TIGHT (`slot != newName`) → 207 = real partial → `return false` → PARTIAL marker → sweeper. Sound. `buildFolderParentChangeFilter` is LOOSE (`folderIds IN affected`) → the "treat 207 as benign" fix (`MaterializeMultiStatusException` swallowed) masks a genuine 8k-of-10k partial write (review finding #25).

**Rule:** the recovery policy must match the filter's tightness — don't benignly swallow 207 under a loose filter.

## Related

- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[MongoDB forbids $lookup inside update pipeline (WriteError 72)]]
- [[Encode a benign-error decision in a dedicated exception type, not a swallowed catch on a magic status code]]
- [[luz_docs parent-change cascade tightened with setEquals slot-differs expr to make 207 diagnostic]]
- [[Materialize code review report - sprint-156 findings index]]
- [[materialize-code-review]]
