---
title: "A link-following crawl pulls in graph-adjacent but topically-tangential nodes"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — /gather LUZ-158390"
tags: [crawl, graph, relevance, scope, agentic]
---

# A link-following crawl pulls in graph-adjacent but topically-tangential nodes

A bounded link-following crawl (frontier over Jira/Confluence links) follows **every in-scope edge regardless of semantic relevance**, so it surfaces nodes that are graph-adjacent but off-topic. Real example: gathering the eArchive story LUZ-158390 pulled in the "Agentic Framework" tooling stories LUZ-159671 (S2 Testing Agent) and LUZ-159670 (S1) — because LUZ-159671 has a **"relates to" issue link** to LUZ-158390 (the Testing Agent USES that ticket as its crawl target), and LUZ-159670 came one hop further (159671 is-blocked-by 159670) only because depth=2.

Not a bug — the edges are real. But "related in the Jira graph" ≠ "relevant to the feature". Controls:
- **Depth** is the bluntest filter: depth 1 keeps only direct links; each extra hop widens topical drift fast.
- **Link-type scope**: treat edge kinds differently — follow `blocks`/`is blocked by` (true dependencies) but NOT `relates to` (associative). The issue-link `type.name` is available in the Jira payload, so classification can gate follow-vs-record per relationship.
- **Relevance pass**: a later step (knowledge refinement / an LLM relevance judge) flags off-topic nodes rather than the crawl trying to be smart.

Design takeaway: separate "reachable" from "relevant". The gather loop should be complete+cheap (follow structural edges, dedup, bound); relevance filtering belongs to scope config or a downstream judge, not the crawl.

Context: kga gather loop (LUZ-159671 test-agent).

## Related

- [[Knowledge-Gathering loop is a bounded frontier crawl with a verify edge]]
