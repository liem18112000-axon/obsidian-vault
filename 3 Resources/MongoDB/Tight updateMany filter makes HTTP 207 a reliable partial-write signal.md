---
title: "Tight updateMany filter makes HTTP 207 a reliable partial-write signal"
created: 2026-06-09
type: lesson
status: seedling
source: "luz-docs materialize code review 2026-06-09"
tags: [mongodb, idempotency, multi-status, cascade, design]
---

# Tight updateMany filter makes HTTP 207 a reliable partial-write signal

When a MongoDB `updateMany` runs through an HTTP/REST layer that returns **207 multi-status** on `matchedCount > modifiedCount`, you can make that 207 a *reliable* signal of a genuine partial write — but only if the **filter is tight**.

**Tight filter:** match ONLY documents that still need the change. Use a `$expr` that compares the stored value to the freshly-computed target (e.g. `$setEquals` / `$ne`). On a converged run the update is deterministic, so **matched == modified**, and any 207 unambiguously means a real partial failure → safe to retry / roll back.

**Loose filter (anti-pattern):** match all candidates (e.g. `folderIds $in affected`). Then idempotent re-stamps make `modified < matched` the *normal* case, so 207 is indistinguishable from a true partial write. Counts alone cannot tell "correctly unchanged" from "failed to change" — treating 207 as benign silently drops stragglers; treating it as failure rolls back correct docs.

**Caveat:** the matched==modified invariant only covers the field(s) the FILTER inspects. If the update pipeline also writes derived fields the filter ignores, those can still drift out of sync — the 207 signal says nothing about them.

This is the deciding factor between "retry/rollback on 207" vs "swallow 207 as no-op": the recovery policy must match the filter's tightness.

## Related

- [[MongoDB forbids $lookup inside update pipeline (WriteError 72)]]
