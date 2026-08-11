---
ai_hash: b006d05143cc41cc
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack credential mirror
status: seedling
tags:
- forms
- mapping
- silent-bug
- vinnstack
title: Mirror form values by the SOURCE's real field keys, not assumed canonical names
type: gotcha
---

# Mirror form values by the SOURCE's real field keys, not assumed canonical names

When copying a values bag across a boundary (form → DB, provider → account), read the keys the SOURCE actually uses, not the canonical names of the destination. A generic `save({ jiraEmail: v.jiraEmail })` where the form field is really `v.email` compiles, runs, throws nothing, and silently mirrors `undefined` into every column — the destructive-looking no-op. Here each provider had its own field keys (atlassian {email, token}, bitbucket {username, token}); the mirror assumed {jiraEmail, jiraApiToken, bitbucketUsername, …} and captured nothing.

Fix: branch the mapping on the source identity (`provider === "atlassian" ? {jiraEmail: v.email, jiraApiToken: v.token} : …`). General guard: a mapping layer between two differently-keyed schemas needs a test that asserts the DESTINATION receives the mapped values — an all-undefined write passes a loose `objectContaining({})` and hides the bug (the original test even fed the wrong keys, so it "passed" while proving nothing).

## Related

- [[Idempotency guards keyed on object presence break when hydration materializes the object]]

%% ai-graph-start %%

**Related notes:**
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
- [[Secondary-write failures should fail loud when their silent version was the actual bug]]
- [[Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected]]
- [[Keep audit updatedBy a role token, not a user identity, when it feeds commit messages or mirrors]]
- [[Per-account write silently skipped when the server cant resolve the session looks saved, isnt]]

%% ai-graph-end %%