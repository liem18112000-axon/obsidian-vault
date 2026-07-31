---
title: "Guard array-typed React state seeded from a fetch with ?? []"
created: 2026-07-26
type: lesson
status: seedling
source: "vinnstack cloud-build fix, session 2026-07-26"
tags: [react, typescript, gotcha, defensive-coding]
---

# Guard array-typed React state seeded from a fetch with ?? []

React state typed as an array (`useState<T[]>([])`) that is later reassigned from a fetch response must guard the assignment with `?? []`. A handler like `if (j.ok) setVersions(j.exports)` looks safe, but if the API returns `{ok:true}` while omitting `exports` — a partial-but-ok response, extremely common in test mocks with a catch-all `return {ok:true}` — the state becomes `undefined` and the very next render crashes at `versions.length` or `versions.slice(...)`.

Fix: `setVersions(j.exports ?? [])`. Defend the array invariant at the assignment; a cosmetic list should degrade to empty, never take the whole surface down.

Real case (vinnstack, 2026-07): both `MdExportControl` and `VersionChanges` read the `/api/export/md` list this way and both crashed under a test mock that returned `{ok:true}` with no `exports`.

See also [[A render crash masks latent crashes elsewhere in the same React subtree]].

## Related

- [[A render crash masks latent crashes elsewhere in the same React subtree]]
