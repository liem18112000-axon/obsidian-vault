---
ai_hash: d883eb33971e89b7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack relay broke local login
status: seedling
tags:
- relay
- headless
- cli
- ux
- vinnstack
title: Gate a headless-only relay behind an explicit env flag so local browser login
  still works
type: lesson
---

# Gate a headless-only relay behind an explicit env flag so local browser login still works

A web-UI CLI-login relay is a REMOTE/headless feature: it PTY-spawns via `script` (Linux-only) and exists because the operator's browser can't reach the server. If the UI routes every relay-capable provider through the relay unconditionally, it BREAKS local dev: on Windows `spawn("script")` is ENOENT → relay-start fails → the button shows a spinner then dies; and even on local Mac/Linux the direct browser login (which just works, opening the local browser) is needlessly replaced by a paste-back dance.

Rule: gate relay availability on an explicit headless signal (an env flag like VINNSTACK_HEADLESS=1 set only in the K8s manifest), NOT on `!!provider.relay` alone. `hasRelay = !!p.relay && isHeadless()`. Locally the flag is absent → the button uses the direct browser login (opens the local browser); on the pod the flag is set → the relay dialog appears. Deterministic, and a dev can opt into testing the relay locally by setting the flag.

Symptom that points here: "Sign in shows a spinner then stops, nothing else" in LOCAL dev after adding a relay; check for `spawn script ENOENT`.

## Related

- [[3 Resources/Backend/Node/Interactive OAuth CLIs need a PTY - wrap in script(1), force wide cols, strip ANSI to parse the URL]]

%% ai-graph-start %%

**Related notes:**
- [[Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry]]
- [[Arm a new login gate by env presence so shipping auth cannot lock the operator out]]
- [[Vinnstack desktop app dropped Google OAuth for a typed-email operator identity]]
- [[Vinnstack auth providers two patterns and the rule for adding one]]
- [[Prefer pasting a token minted once over scraping it from a PTY relay]]

%% ai-graph-end %%