---
title: "Knowledge-Gathering loop is a bounded frontier crawl with a verify edge"
created: 2026-08-27
type: model
status: seedling
source: "session 2026-08-27 — test-agent proposal"
tags: [agentic, crawl, loop-spec, luz-159671]
---

# Knowledge-Gathering loop is a bounded frontier crawl with a verify edge

The **Knowledge-Gathering** loop of an agent that has to "read everything linked from a seed" is best modeled as a **bounded frontier crawl**, not a single fetch:

`Seed → Fetch → Extract links → Classify (follow vs record-only) → Expand frontier (dedup via a `visited` set) → Distill to a markdown note → Persist → Verify edge → (loop | return)`.

Key properties that make it correct rather than just a scraper:

- **Bounded.** Terminate when ANY holds: frontier empty · depth reached · `max_nodes` · `max_seconds` · N consecutive "dry" rounds (nothing new discovered). Without this it never converges on a link-rich graph.
- **Dedup with a visited set** keyed on the *canonical* URL, so cycles (A links B links A) do not loop forever and re-fetch.
- **Verify edge** (borrowed from the loop-spec of the parent Testing-Agent story): before returning, cheaply re-check suspect/broken links so a transient failure does not poison memory — re-check, do not blindly re-crawl.
- **Nothing dropped silently** — the load-bearing design rule. An out-of-scope link is *recorded* (not followed); a broken link is *flagged*; an unreachable node becomes a *declared gap*, never an omission. A gap is a flagged row, not an absent one.
- **Distill-then-persist per node** turns a transcript into *curated facts* — one markdown note per node with provenance frontmatter + a links table, plus a run-log line (inputs/sources/output/confidence).

Context: designed for the LUZ-159671 Testing-Agent first slice (Atlassian → GCS markdown memory bank).

## Related

- [[A remote A2A agent needs its own connectors because MCP is client-side]]
