---
ai_hash: 0ce754a8dfbe5510
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- KLARA web availability probe needs curl -L because healthy root answers 302 empty
created: 2026-07-21
entities: []
source: perf maintenance watch 2026-07-21
status: seedling
tags:
- klara
- curl
- keycloak
- gotcha
- monitoring
title: KLARA web availability probe must follow redirects and inspect the final URL
type: lesson
---

# KLARA web availability probe must follow redirects and inspect the final URL

A curl probe of a KLARA web env root can't judge health from the first response: the root often answers `302` with an EMPTY body regardless of whether the env is usable. Probe with `curl -skL` and classify by the FINAL landing:

- `200` at root with "server is not available" / "working hard to improve KLARA" text → classic maintenance page.
- redirect ending at `…/ch.klara.luz.components.KeyCloakErrorPage/KeyCloakErrorPage.xhtml` (HTTP 200) → **Keycloak/SSO is broken**; the browser renders the same "server is not available" message there, so the env is still unusable even though root "responds". Observed on performance.klara.tech 2026-07-21 (90+ min).
- redirect ending at a Keycloak login URL → env actually up.

Two watcher bugs to avoid: grepping the un-followed root body (302/empty misreads as down-or-up depending on your guard), and treating "root no longer serves maintenance text" as recovered — the error moved behind the redirect.

## Related

- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]

%% ai-graph-start %%

**Related notes:**
- [[klara-prod is a separate GCP project, not a namespace]]
- [[Per-pod breakdown of rejections separates a bad replica from a global config problem]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]
- [[Log red herrings enclosing class name and baseline-noise lines]]
- [[Istio DC response_flag with round latency = caller read timeout]]

%% ai-graph-end %%