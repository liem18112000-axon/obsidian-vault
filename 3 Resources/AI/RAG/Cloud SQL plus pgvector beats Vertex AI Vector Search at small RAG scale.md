---
title: "Cloud SQL plus pgvector beats Vertex AI Vector Search at small RAG scale"
created: 2026-07-10
type: lesson
status: evergreen
source: "deep-research pass, virtual-avatar project, 2026-07-10"
tags: [rag, pgvector, gcp, vector-search, architecture]
---

# Cloud SQL plus pgvector beats Vertex AI Vector Search at small RAG scale

For a small-scale RAG knowledge base — one presenter/topic's worth of material, hundreds to low-thousands of chunks, not enterprise scale — Cloud SQL for PostgreSQL with the `pgvector` extension is the right-sized store, not Vertex AI Vector Search.

Vertex AI Vector Search adds separate infrastructure (dedicated index build, streaming index updates, its own storage/compute billing) that only pays off once you're past roughly tens of millions of vectors at high query throughput. Below that, it's pure overhead: more moving parts to operate, for no retrieval-quality benefit at small scale. Plain Postgres + pgvector gives you SQL-native vector search, easy metadata filtering, and joins with relational data, all in infrastructure you likely already run.

AlloyDB (with AlloyDB AI) is the natural middle upgrade step if Cloud SQL's performance ceiling becomes a real issue (e.g. going multi-tenant with many concurrent topics/high QPS), before jumping all the way to a dedicated vector-search service.

General heuristic worth keeping: don't reach for a dedicated vector-search service (Vertex AI Vector Search, Pinecone, etc.) until plain Postgres+pgvector has actually been shown to fall short — most solo-developer or small-team RAG use cases never hit that ceiling.

## Related
- [[Virtual avatar presenter project design plan]]

## Related

- [[Virtual avatar presenter project design plan]]
