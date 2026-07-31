---
title: "Separate read-check from create in a first-run onboarding gate"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [onboarding, gating, api-design, vinnstack]
---

# Separate read-check from create in a first-run onboarding gate

When gating an app behind "has this identity completed setup," split the check into two distinct operations: a read-only GET that only checks whether a persisted record exists (and activates it in-memory if so, without creating anything), and a separate POST that the onboarding form calls on successful submit to actually create the record.

Why this matters: if the same endpoint that CHECKS for the record also auto-creates it as a side effect (e.g. "return existing row, or an empty placeholder if none"), the gate becomes useless — the very act of checking silently satisfies the gate condition, so the onboarding screen would only ever show for a single instant before the check itself creates the row.

In the Vinnstack project, this showed up concretely in `/api/account/activate`: the route used to do `getAccountCredentials(email) ?? { accountId: email, email }` and activate that either way, which meant a first-time visitor would already look "onboarded" as soon as the page made its first fetch. Splitting it into GET (read-only, reports `found: true/false`) and POST (called only from the onboarding Save button, actually persists the row) fixed this — the gate component (`OnboardingGate.tsx`) renders the full-screen onboarding wizard instead of the app until `found` is true, and `found` only flips once the operator actually completes the form.

Related: this pairs with the general principle that a "does X exist" check and "ensure X exists" are different operations with different call sites, even when the underlying logic (a merge/upsert) is shared.
