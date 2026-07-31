---
title: "deleteMany over kubectl port-forward runs about 5k docs per second"
created: 2026-06-11
type: observation
status: seedling
source: "session 2026-06-11 (first earchive-data-clean run)"
tags: [luz, mongodb, performance, earchive]
---

# deleteMany over kubectl port-forward runs about 5k docs per second

Calibration point for estimating tenant-clean durations on the Luz dev MongoDB clusters: `deleteMany({})` of 128,202 eArchive documents (rich ~production-shaped docs with `documentTextContent` lorem paragraphs) through a `kubectl port-forward` to the replica primary took 24.3 s — roughly 5,000 docs/s. The 88-folder collection cleared in 0.2 s.

So a full canary-sized wipe is ~25 s end to end; budget minutes only for multi-million-doc tenants. Measured 2026-06-11 on tenant b260a45a (luz-mongodb03-cluster-rs-0) via the earchive-data-clean skill.

## Related

- [[Destructive Luz skills use a preview-first CONFIRM gate]]
- [[3 Resources/Work-Kepler/luz-docs/skills/eArchive dev skills are self-contained copies, not shared helpers]]
