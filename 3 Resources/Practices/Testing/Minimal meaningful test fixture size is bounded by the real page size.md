---
ai_hash: c580a14deb1b48eb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26
status: seedling
tags:
- testing
- integration-test
- pagination
- luz-docs
title: Minimal meaningful test fixture size is bounded by the real page size
type: lesson
---

# Minimal meaningful test fixture size is bounded by the real page size

When testing paginated/batched code, you might be tempted to make a fixture large enough to cross the real pagination boundary. That is only practical if the page size is small. In luz-docs the delete-folder discovery page size is `Constants.SEARCH_PAGE * 20` = **500 * 20 = 10000**, so crossing it would need 10001+ documents — far too many to create in an integration test.

So 'minimal meaningful' fixture size is bounded by two competing limits:
- **lower bound:** more than one item, or the batch path is bypassed entirely (see related note),
- **upper bound:** small enough to create cheaply in the test environment.

When the page size sits above the practical upper bound, accept that the IT cannot cover the page-boundary case and cover it with a unit test (or a direct DB seed) instead. Verify *batch correctness* in the IT with a handful of items; leave *pagination-boundary correctness* to a cheaper layer.

Related: [[Bulk write paths in folder delete only engage with more than one document]]

## Related

- [[Bulk write paths in folder delete only engage with more than one document]]

%% ai-graph-start %%

**Related notes:**
- [[Bulk write paths in folder delete only engage with more than one document]]
- [[luz_docs FolderDeletingServiceIT coverage gaps]]
- [[luz-docs delete-folder batching roadmap - remaining per-item paths]]
- [[luz-docs delete detail-response field for removed links is removedLinkParents not removedLink]]
- [[Black-box test missing-_shard count correctness with a delta test]]

%% ai-graph-end %%