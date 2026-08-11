---
ai_hash: 63396e6ad55d7e85
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-29
entities: []
source: Vinnstack debugging session 2026-06-29
status: seedling
tags:
- error-handling
- fallback
- observability
- design
- gotcha
title: A silent canned fallback masks real failures — surface the underlying error
type: lesson
---

# A silent canned fallback masks real failures — surface the underlying error

When a feature degrades to a fallback path, that fallback must **surface the real failure reason**, never substitute plausible-looking canned output. A fallback that returns convincing fake content turns a hard error into something indistinguishable from a normal success — so the underlying bug stays invisible (nobody reports "it worked, just wrong").

Concrete case (Vinnstack chat): on any failure to reach the local `claude` CLI, the client called a `simulate()` stub that returned topic-matched canned replies ("Fleet looks healthy — 11 agents live…"). A genuine spawn `ENOENT` therefore looked like a real status answer. The CLI had been unreachable for every message and no one could tell, because the fallback was *too* convincing.

The fix pattern: capture the actual error channel during the operation (here: the stream's `stderr` text, `error` event `message`, and non-zero exit `code`), then on the fallback show a clearly-marked diagnostic with that real detail and a remediation hint — instead of fabricated content. Prioritize the most specific signal: explicit error detail > exit code > caught exception message > a generic "no output" default.

Rule of thumb: a fallback may be graceful, but it must be **honest** — degrade to a visible error, not to fiction. If you can't distinguish your fallback output from a real success at a glance, the fallback is hiding bugs.

## Related
[[3 Resources/Languages/Node.js/Node spawn shellfalse on Windows won't run .cmd.ps1 wrappers (ENOENT)]]

## Related

- [[3 Resources/Languages/Node.js/Node spawn shellfalse on Windows won't run .cmd.ps1 wrappers (ENOENT)]]

%% ai-graph-start %%

**Related notes:**
- [[Node spawn shellfalse on Windows won't run .cmd.ps1 wrappers (ENOENT)]]
- [[Never cache a negative fallback in the same slot as a resolved value]]
- [[Classify stream failures on the server, not the client]]
- [[Headless claude exit 0 does not mean the operation succeeded]]
- [[Spawning a prompting CLI hangs on open stdin — use stdio stdin ignore for EOF]]

%% ai-graph-end %%