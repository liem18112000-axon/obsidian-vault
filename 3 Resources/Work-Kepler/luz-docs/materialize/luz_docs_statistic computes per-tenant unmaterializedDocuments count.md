---
title: "luz_docs_statistic computes per-tenant unmaterializedDocuments count"
created: 2026-07-15
type: reference
status: seedling
source: "luz_docs materialize-gate reliability research, 2026-07-15"
tags: [luz-docs, microservice, reference, statistics]
---

# luz_docs_statistic computes per-tenant unmaterializedDocuments count

A separate microservice, luz_docs_statistic (repo C:\Users\dvtliem\Kepler\luz_docs_statistic, package ch.klara.luz.docs.statistic), already computes and persists a per-tenant unmaterializedDocuments count: a full $facet Mongo aggregation over the documents collection, run by an EJB timer every 1 minute (gated by pending Pub/Sub events per tenant, so only tenants with a recent triggering event get recomputed that cycle), stored in a documentstatistics Mongo collection, and exposed read-only via GET /{service-tenant-id}/document-statistic/{tenant-id}.

This is a ready-made, already-running, already-persisted ground-truth signal — useful for reconciling against luz_docs's own materialize-campaign completion status instead of building a new count query or new schedule from scratch.

Caveat found: luz_docs_statistic's unmaterialized filter checks 4 fields (_isPublic, _effectiveSecurityClassCodes, _folderNames, _folderSecurityClassCodes) while luz_docs's own MaterializeRepository.isMaterialized / buildUnmaterializedFilter() checks only 3 (missing _folderSecurityClassCodes) — the two services currently disagree on what "unmaterialized" means, and must be aligned before cross-checking one against the other. This is a concrete instance of the general "two filters over the same concept must cover the same field set" lesson.

## Related

- [[Migration campaign status can silently drift from real document state]]
- [[Fan-out gate and backfill filter must cover the same field set]]
