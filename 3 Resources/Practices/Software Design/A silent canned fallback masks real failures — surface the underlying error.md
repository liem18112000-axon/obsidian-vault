---
title: "A silent canned fallback masks real failures — surface the underlying error"
created: 2026-06-29
type: lesson
status: seedling
source: "Vinnstack debugging session 2026-06-29"
tags: [error-handling, fallback, observability, design, gotcha]
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
