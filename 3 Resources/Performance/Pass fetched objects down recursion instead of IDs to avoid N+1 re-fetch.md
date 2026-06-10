---
title: "Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch"
created: 2026-06-10
type: howto
status: seedling
source: "session 2026-06-10"
tags: [performance, n-plus-one, recursion, api-design]
---

# Pass fetched objects down recursion instead of IDs to avoid N+1 re-fetch

When a tree/graph walk fetches children with a list query that already returns full documents, pass those objects down the recursion — don't pass IDs and re-fetch each node by ID inside the recursive call. The ID-based shape silently costs one extra round-trip per node (N+1 on top of the list queries).

Recipe: keep the public String-ID method as a thin wrapper (dedup-check → fetch → delegate) and add a private overload taking the already-fetched object; the recursion calls the object overload.

Precondition to verify first: the list query's projection must match the by-id fetch (same fields), otherwise downstream readers of the object may miss fields. Check what projection the list endpoint applies before relying on its results.

Seen concretely in [[luz-docs folder delete filter double-fetched every subfolder]].

## Related

- [[luz-docs folder delete filter double-fetched every subfolder]]
