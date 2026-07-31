---
title: "Tight update filter makes Mongo matched-vs-modified count diagnostic"
created: 2026-06-08
type: lesson
status: seedling
source: "luz_docs #25 sprint-158"
tags: [mongodb, bulk-update, idempotency, gotcha, luz-docs]
---

# Tight update filter makes Mongo matched-vs-modified count diagnostic

A deterministic bulk update (same input always yields the same result) reports `matchedCount > modifiedCount` whenever the filter matches rows that are *already* at the target value — re-applying the update is a no-op on them. MongoDB/jsonstore surfaces this as **HTTP 207 (MULTI_STATUS)**.

The consequence: under a **loose** filter (one that selects a superset, e.g. `{field:{$in:[...]}}`), a 207 is *expected on normal success* and is **indistinguishable** from a genuine partial write where some rows failed to update. Counts alone cannot disambiguate the two.

**Lesson:** if you want matched-vs-modified to be a reliable health signal (matched==modified ⇒ full success, matched>modified ⇒ real partial failure), the filter must select **only rows that genuinely need the write** — add a predicate comparing the current value to the target. A tight filter makes the count diagnostic; a loose one makes it noise. Don't 'benignly swallow' 207 under a loose filter — you'll also swallow real partial failures.

This is why luz_docs' folder-**rename** cascade (tight filter: slot != newName) can treat its counts as sound, while the folder-**parent-change** cascade originally could not (loose `$in` filter) — see [[luz_docs parent-change cascade tightened with setEquals slot-differs expr to make 207 diagnostic]].

## Related

- [[Materialize code review report - sprint-156 findings index]]
