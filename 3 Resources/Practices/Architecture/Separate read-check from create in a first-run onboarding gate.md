---
ai_hash: d25bac9bb802a0d3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: Vinnstack session 2026-07-07
status: seedling
tags:
- onboarding
- gating
- api-design
- vinnstack
title: Separate read-check from create in a first-run onboarding gate
type: lesson
---

# Separate read-check from create in a first-run onboarding gate

When gating an app behind "has this identity completed setup," split the check into two distinct operations: a read-only GET that only checks whether a persisted record exists (and activates it in-memory if so, without creating anything), and a separate POST that the onboarding form calls on successful submit to actually create the record.

Why this matters: if the same endpoint that CHECKS for the record also auto-creates it as a side effect (e.g. "return existing row, or an empty placeholder if none"), the gate becomes useless — the very act of checking silently satisfies the gate condition, so the onboarding screen would only ever show for a single instant before the check itself creates the row.

In the Vinnstack project, this showed up concretely in `/api/account/activate`: the route used to do `getAccountCredentials(email) ?? { accountId: email, email }` and activate that either way, which meant a first-time visitor would already look "onboarded" as soon as the page made its first fetch. Splitting it into GET (read-only, reports `found: true/false`) and POST (called only from the onboarding Save button, actually persists the row) fixed this — the gate component (`OnboardingGate.tsx`) renders the full-screen onboarding wizard instead of the app until `found` is true, and `found` only flips once the operator actually completes the form.

Related: this pairs with the general principle that a "does X exist" check and "ensure X exists" are different operations with different call sites, even when the underlying logic (a merge/upsert) is shared.

%% ai-graph-start %%

**Related notes:**
- [[Arm a new login gate by env presence so shipping auth cannot lock the operator out]]
- [[Array.every on an empty array is true - gate on existence before completeness]]
- [[Seed-in-memory-but-persist-on-save leaves no row when a prior layer already shows connected]]
- [[Idempotency guards keyed on object presence break when hydration materializes the object]]
- [[Per-account write silently skipped when the server cant resolve the session looks saved, isnt]]

%% ai-graph-end %%