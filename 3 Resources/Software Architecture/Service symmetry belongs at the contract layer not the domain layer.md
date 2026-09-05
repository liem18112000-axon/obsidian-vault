---
title: "Service symmetry belongs at the contract layer not the domain layer"
created: 2026-08-31
type: argument
status: seedling
source: "session 2026-08-31 test-agent refactor"
tags: [architecture, design-principle, solid, package-structure]
---

# Service symmetry belongs at the contract layer not the domain layer

When two sibling services do a similar *kind* of job, align them on their **contract/plumbing layer** (the framework-imposed shape) and let their **domain-engine layer** diverge freely. Forcing the domain packages into a common shape is cosmetic symmetry that fights the problem domain; forcing the plumbing to differ is needless divergence.

**Why:** the plumbing exists to satisfy an external contract (a protocol, a framework, a deploy target) — it is the same for every service of that class, so identical structure there aids navigation and dedup. The domain engine exists to solve one service'\''s specific problem — its shape should follow *that* problem, and a service that does more will simply have more packages.

**Litmus test:** ask of each shared-vs-divergent decision, "is this dictated by the framework/contract, or by what this service uniquely does?" Contract → align. Domain → let it differ. A telling signal is generic vs specific work migrating in opposite directions: generic helpers hoist into a shared package, domain-specific ones stay local.

See [[test-agent two A2A agents share a skeleton but diverge in domain engines]] for a worked example.

## Related

- [[test-agent two A2A agents share a skeleton but diverge in domain engines]]
