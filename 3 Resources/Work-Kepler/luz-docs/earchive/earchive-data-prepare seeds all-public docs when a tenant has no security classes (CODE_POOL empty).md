---
ai_hash: 93af91d534866453
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities: []
source: session 2026-06-23 eArchive seed
status: seedling
tags:
- earchive
- luz-docs
- seeding
- security-class
- gotcha
title: earchive-data-prepare seeds all-public docs when a tenant has no security classes
  (CODE_POOL empty)
type: lesson
---

# earchive-data-prepare seeds all-public docs when a tenant has no security classes (CODE_POOL empty)

The `earchive-data-prepare` skill fetches the tenant's security classes from the api-forwarder (localhost:8080) into `CODE_POOL` before generating data. If that fetch returns nothing, the skill logs `[fetch-classes] FAILED: security_classes claim missing or empty — CODE_POOL=[]` and proceeds with an **empty CODE_POOL**.

**Consequence:** with `CODE_POOL=[]`, no document or folder gets any security-class code, so every seeded doc is effectively **public/unrestricted** — `restrictedFolderPct` / `restrictedDocPct` have nothing to draw from. The run still succeeds; it just cannot produce restricted-with-codes docs.

**When it happens:** the tenant simply has no security classes defined (observed on dev tenant `e5c1f5f5-...c8706`, 2026-06-23). By contrast a tenant with classes yields a populated pool, e.g. `["TOPSECRET_1","DEVELOPMENT_2","VISITER_3","ADMIN_4"]`.

**So:** if a test needs restricted docs or materialise-with-codes coverage, confirm `CODE_POOL` is non-empty in the prepare log first; an empty pool means define security classes on the tenant before seeding.

Relates to [[directConnection=true counts read only the connected node and can be stale on a secondary]].

## Related

- [[directConnection=true counts read only the connected node and can be stale on a secondary]]

%% ai-graph-start %%

**Related notes:**
- [[earchive-data-prepare wrapper exits 0 even when the generator dies mid-run (verify the log footer)]]
- [[Empty per-folder codes means public, not no-access]]
- [[Materialize gate must require _shard or parallelized count undercounts]]
- [[eArchive dev skills are self-contained copies, not shared helpers]]
- [[Fail-closed defense over a parallel array distinguish present-but-short from absent]]

%% ai-graph-end %%