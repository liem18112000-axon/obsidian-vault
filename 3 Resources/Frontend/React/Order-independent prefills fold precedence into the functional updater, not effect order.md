---
title: "Order-independent prefills: fold precedence into the functional updater, not effect order"
created: 2026-07-04
type: lesson
status: seedling
source: "session 2026-07-04, vinnstack operator profile seeding"
tags: [react, effects, state, prefill]
---

# Order-independent prefills: fold precedence into the functional updater, not effect order

When two useEffects seed the same state from different sources (localStorage on mount; a session that may resolve before OR after mount when cached), relying on effect declaration order for precedence is a race: with a cached session, the lower-precedence seeder can run first and then get clobbered by the mount effect - or vice versa.

Make each seeding effect ORDER-INDEPENDENT by expressing the full precedence chain inside the functional updater:

    setName((cur) => cur || getSaved() || session.name || "");

`cur` (what the user typed) beats saved profile beats remote default, regardless of which effect fires first, and the update is idempotent so re-runs are harmless. General rule: precedence belongs in the VALUE computation, never in effect scheduling.

## Related

- [[Validate fetch response shape in hooks - catch-all test mocks return wrong bodies]]
