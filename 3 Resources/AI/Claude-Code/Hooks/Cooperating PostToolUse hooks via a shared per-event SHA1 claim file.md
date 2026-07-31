---
title: "Cooperating PostToolUse hooks via a shared per-event SHA1 claim file"
created: 2026-06-02
type: concept
status: seedling
source: "session 2026-06-02"
tags: [claude-code, hooks, powershell, design-pattern]
---

# Cooperating PostToolUse hooks via a shared per-event SHA1 claim file

Two PostToolUse hooks that both match the same `Edit|Write|MultiEdit` event will *each* inject their instruction block, double-prompting the user. To make them mutually exclusive â€” **at most one fires per edit** â€” have both hooks compute a stable per-event key and coordinate through a shared file.

The key: SHA1 of the raw stdin payload. The harness pipes the *same bytes* to every hook on the event, so each process independently derives an identical hash. No clock, no IDs, no shared memory needed.

The protocol (`state/claim-<sessionId>.json` = `{eventHash, gate}`):
- Before firing, a gate reads the claim. If `claim.eventHash == current && claim.gate != self` â†’ **DEFER**: persist accumulated counters but do **not** reset and do **not** spend a pass. It fires on the next qualifying edit instead.
- Otherwise it writes the claim with its own name, then fires.

Effect: the two gates naturally **alternate** across a session, and the deferring gate loses nothing because its cumulative counters stay intact. Both gates must implement the check *and* the write for cooperation to be symmetric.

Concrete instance: `simplify-gate` (minimize code) + `reusable-gate` (DRY/reuse review of newly-added code) at `C:\Users\dvtliem\.claude\hooks\`. `reusable-gate` fires when cumulative files > 2 OR lines > 3, capped at `maxLoops`, and carries an `ignore` substring list (`\node_modules\`, `\.venv\`, `\site-packages\`, `\dist\`, `\build\`, `\target\`, `.min.js`, â€¦) so its review never reaches into dependencies/vendored/generated/build output â€” scope stays on current-project source.

Gotcha when *testing* this: two separate stdin-encoding traps corrupt the payload or the hash.

## Related

- [[Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON]]
- [[PowerShell pipe appends a newline to native-command stdin, shifting any hash]]

