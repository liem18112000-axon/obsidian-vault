---
title: "eArchive dev skills are self-contained copies, not shared helpers"
created: 2026-06-11
type: concept
status: seedling
source: "session 2026-06-11 (creating earchive-data-clean)"
tags: [luz, earchive, mongodb, skills, convention]
---

# eArchive dev skills are self-contained copies, not shared helpers

Every skill in the Luz/eArchive dev-skill family (earchive-data-prepare, earchive-materialize-index, earchive-data-clean, luz-skill-materialize-stats) carries its own full copy of the MongoDB replica-primary discovery machinery instead of sourcing a shared helper script. The duplication is deliberate: skills are packaged self-contained so renaming or deleting one skill can never silently break another.

The shared machinery being copied:

1. Derive the cluster: first hex char of `TENANT_ID` (decimal) % 4 → `luz-mongodb0X-cluster-rs`.
2. For idx 0,1,2: `kubectl port-forward <sts>-<idx> 27017` (namespace `dev-mongodb-clusters`), wait for TCP.
3. Probe with a one-shot insert+drop on a namespaced collection named `_<skill-name>_probe` — never touches real collections.
4. Exit-code contract: 0 = primary found, 2 = secondary (try next replica), 1 = error.

When adding a new skill to the family, copy the loop from an existing sibling, rename the probe collection, and trim deps to what the skill actually needs (e.g. earchive-data-clean drops `@faker-js/faker` because it generates nothing).

## Related

- [[Destructive Luz skills use a preview-first CONFIRM gate]]
