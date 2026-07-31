---
ai_hash: 7ed9a07611ce733d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-06
entities: []
source: Vinnstack session 2026-07-06
status: seedling
tags:
- credential-design
- multi-tenant
- vinnstack
title: Per-account credential store should only hold per-identity secrets
type: lesson
---

# Per-account credential store should only hold per-identity secrets

A per-account credential store (keyed by signed-in identity, e.g. a Google `sub`) should only carry fields that genuinely differ per identity. If a field is actually machine-scoped or environment-scoped — the same value for every user on that machine/deployment — storing it per-account is redundant duplication of a config source, not a feature.

In the Vinnstack project this showed up concretely: an `account_credentials` DB table (added for Google OAuth login) was holding both a Claude relay OAuth token AND Jira/Bitbucket credentials. The Claude token genuinely varies per signed-in account (each Google identity needs its own `claude` CLI session/config dir). Jira/Bitbucket did not — they are shared team credentials tied to the *machine*, not to *who is signed in*. The fix was to strip Jira/Bitbucket entirely out of the per-account store and DB table, leaving them sourced only from the machine config file (`config.json`) / environment variables, while keeping the Claude token as the one legitimately per-account field.

Signal to watch for: when a "per-X" store accumulates fields alongside the ones that actually vary by X, ask whether each field really needs X-scoping, or whether it is just riding along because it was convenient to add to an existing save/load path.

%% ai-graph-start %%

**Related notes:**
- [[A process-global active account leaks identity across concurrent requests in a single-process server]]
- [[Vinnstack desktop app dropped Google OAuth for a typed-email operator identity]]
- [[Per-account CLI sessions inject CLAUDE_CONFIG_DIR and CLOUDSDK_CONFIG at the spawn-env chokepoint]]
- [[Local-app provider sign-in drive the vendor CLI; Vertex is the exception (gcloud ADC + projectregion)]]
- [[Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected]]

%% ai-graph-end %%