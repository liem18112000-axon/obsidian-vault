---
ai_hash: d26a17ccb6681dce
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: Vinnstack session 2026-07-07
status: seedling
tags:
- security
- allowlist
- database
- vinnstack
title: A hardcoded allowlist becomes a security boundary, not just data, once it gates
  credentialed access
type: lesson
---

# A hardcoded allowlist becomes a security boundary, not just data, once it gates credentialed access

A list that looks like plain configuration data — a set of allowed repo names, hostnames, or IDs — can secretly be a security control if code uses it to gate access to a credential or a privileged action. Migrating that data from hardcoded source into a database table is not just a refactor: it silently moves the enforcement boundary from "requires a reviewed code change" to "requires only DB write access," which is usually a much wider set of people/processes.

Concretely: in Vinnstack, `lib/graphifyRunner.ts` hardcodes `REPO_SLUGS`, a list of 18 repos explicitly documented as security-reviewed ("read access verified", referencing an internal Confluence audit). The list gates which repos may have the Bitbucket app-password credential used against them to download a tarball and be scanned. When asked to "migrate this repo+url+status info to a database table," the literal request (mirroring an existing DB-as-source-of-truth pattern already used elsewhere in the same codebase for a non-security list, `process_flow_products`/`journeys`) would have quietly converted the allowlist from code-reviewed to DB-editable.

The general lesson: before moving a hardcoded list into a runtime-editable store, ask what that list actually *gates* — display order and reporting data is safe to migrate outright; anything that authorizes use of a credential, tool invocation, or privileged code path deserves an explicit decision (and probably a design doc) before the enforcement boundary moves, even when a same-shaped migration elsewhere in the codebase makes it look routine. See [[Graphify acquires repo source gitless via Bitbucket tarball download]] for the underlying security model this list protects.

## Related

- [[Graphify acquires repo source gitless via Bitbucket tarball download]]

%% ai-graph-start %%

**Related notes:**
- [[Graphify acquires repo source gitless via Bitbucket tarball download]]
- [[Hard-exclude an AI agent from a resource by shrinking its file grant, not by prompting]]
- [[Do not hardcode a real DB password as a source-code fallback for a packaged desktop app]]
- [[Per-account credential store should only hold per-identity secrets]]
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]

%% ai-graph-end %%