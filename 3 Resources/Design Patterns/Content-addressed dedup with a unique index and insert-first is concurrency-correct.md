---
title: "Content-addressed dedup with a unique index and insert-first is concurrency-correct"
created: 2026-08-11
type: concept
status: seedling
source: "luz_docs_import LUZ-158230 · 2026-08-11"
tags: [dedup, idempotency, mongodb, concurrency, hashing]
---

# Content-addressed dedup with a unique index and insert-first is concurrency-correct

For idempotent batch import, make dedup identity **content-addressed** and enforce it with a database **unique index** using **insert-first** semantics:

- Batch identity: `(tenantId, zipSha256)` — SHA-256 of the archive bytes, not its (caller-controlled, non-unique) name.
- Item identity: `(tenantId, contentSha256, normalizedPath)` — SHA-256 of the file bytes + the NFC path.
- Enforcement: `createIndex({tenantId, contentSha256, normalizedPath}, {unique:true})`, then **INSERT before create**; treat the duplicate-key error (Mongo **E11000**) as the "already imported" signal.

**Why it beats read-then-check:** a `contains()` over previously-imported paths is (a) racy under concurrency — two workers both read "absent" and both create — and (b) content-blind, so it cannot tell a *revised* file (new content hash ⇒ should import) from a *true* duplicate (same hash ⇒ skip), and it collapses two unrelated batches that share a name. The unique index makes the datastore the single arbiter; insert-first is atomic.

Compute the hashes cheaply in passes you already do: the archive hash folds into the upload stream (`DigestInputStream`), the per-file hash into the read that the create already performs.

Related: [[NFC-normalize dedup keys so macOS NFD and Windows NFC file re-exports converge]]

## Related

- [[NFC-normalize dedup keys so macOS NFD and Windows NFC file re-exports converge]]
