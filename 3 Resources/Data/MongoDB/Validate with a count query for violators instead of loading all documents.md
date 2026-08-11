---
ai_hash: 1e3b3b1d8d5b0787
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: luz-docs enhance-delete-folder-api, sprint 158 (2026-06-10)
status: seedling
tags:
- mongodb
- fail-fast
- performance
- luz-docs
title: Validate with a count query for violators instead of loading all documents
type: lesson
---

# Validate with a count query for violators instead of loading all documents

When a precondition check only needs to know "does any violating document exist?", replace loading every document and inspecting fields in application code with a **single count query for violators** — build a filter that matches only the violating shape and fail fast when `count > 0`.

Example from luz-docs folder deletion: instead of fetching all documents of a folder to check each `securityClassCode`/`inheritedSecurityClassCode` against the tenant's allowed classes, one `countByFilter` matches documents whose class codes fall outside the allowed set (`$not $in` plus exists-checks), and the service throws `DocumentMismatchSecurityClassCodeException` if the count is positive. Cost drops from O(N) document transfers to one round trip, and the validation runs *before* any pagination work starts.

Caveat: the violating-filter must encode the exact negation of the validity rule, including missing-field semantics ($exists), or violators slip through.

Related: [[Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]

## Related

- [[Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder]]
- [[Split bulk scans on folderIds.1 exists to separate single-array-element fast path]]
- [[Don't share one predicate between a read-path gate and a backfill selector]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]
- [[Production security count is already COUNT_SCAN (covered); benchmark query's FETCH is inherent (multikey+$or+$nin)]]

%% ai-graph-end %%