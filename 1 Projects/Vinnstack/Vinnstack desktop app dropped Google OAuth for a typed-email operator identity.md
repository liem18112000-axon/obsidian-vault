---
ai_hash: b63f2f562bb30bf4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities:
- Vinnstack desktop app
- Google OAuth
- typed-email operator identity
- NextAuth
- Claude relay token
- Electron BrowserWindow
- Google
- disallowed_useragent policy
- system-browser-redirect
- custom-protocol
- one-time-cookie-bridge
- accounts.google.com
- webContents will-navigate
- shell.openExternal
- custom URI scheme
- Google Cloud OAuth consent-screen
- configuration issue
- GCP Console
- account_credentials table
- middleware gating
- Electron OAuth bridge
- login/identity system
- per-user database row
- full OAuth flow
- simple typed identifier
- third party's admin console
source: Vinnstack desktop-packaging session 2026-07-07
status: seedling
tags:
- vinnstack
- electron
- oauth
- architecture-decision
title: Vinnstack desktop app dropped Google OAuth for a typed-email operator identity
type: lesson
---

# Vinnstack desktop app dropped Google OAuth for a typed-email operator identity

In the Vinnstack desktop-app project, the Google OAuth login gate (NextAuth-based "Sign in with Google" whole-app gate, used to key a per-account Claude relay token) was removed and replaced with a plain email text field captured once during onboarding. The email becomes the account_credentials table key directly (no session, no login flow at all).

Why: getting Google OAuth working inside an Electron BrowserWindow required a real workaround (Google blocks embedded browsers via its disallowed_useragent policy), which was solved with a system-browser-redirect + custom-protocol + one-time-cookie-bridge mechanism (see [[Electron OAuth needs a system-browser handoff, not an embedded window]] if that note exists, or the reasoning: intercept navigation to accounts.google.com via webContents will-navigate, shell.openExternal it, then hand the resulting session back into Electron via a registered custom URI scheme). That mechanism worked, but then a live end-to-end test hit an unrelated Google Cloud OAuth consent-screen configuration issue ("Try signing in with a different account", rejected mid-consent for an account that had worked before in a browser) that could not be diagnosed or fixed without GCP Console access. Rather than debug a third-party admin/consent-screen policy blocking progress, the pragmatic call was to drop the OAuth requirement entirely for this desktop app: it is single-operator-per-machine already, so a typed email is sufficient identity, and the whole login mechanism (NextAuth, middleware gating, the Electron OAuth bridge) was dead weight for that use case.

Lesson: when a login/identity system exists only to key a per-user database row (not for real authorization/security), and the actual usage pattern is "one person per machine," a full OAuth flow is often overkill — a simple typed identifier captured once in onboarding does the same job with far less moving parts and no dependency on a third party’s admin console being configured correctly.

%% ai-graph-start %%

**Related notes:**
- [[Local-app provider sign-in drive the vendor CLI; Vertex is the exception (gcloud ADC + projectregion)]]
- [[Per-account credential store should only hold per-identity secrets]]
- [[Vinnstack provider abstraction enables pluggable auth without UIroute changes]]
- [[Vinnstack is local-only by design spawned-CLI login + local FS state + single-tenant]]
- [[Vinnstack auth providers two patterns and the rule for adding one]]

**Relations:**
- Vinnstack desktop app — *DROPPED* — Google OAuth
- Vinnstack desktop app — *ADOPTED* — typed-email operator identity
- Google OAuth — *WAS_BASED_ON* — NextAuth
- Google OAuth — *KEYED* — Claude relay token
- typed-email operator identity — *IS_KEY_FOR* — account_credentials table
- Google OAuth — *REQUIRED_WORKAROUND_IN* — Electron BrowserWindow
- Google — *BLOCKS* — embedded browsers
- Google — *BLOCKS_VIA* — disallowed_useragent policy
- WORKAROUND_FOR_GOOGLE_OAUTH_IN_ELECTRON — *INVOLVED* — system-browser-redirect
- WORKAROUND_FOR_GOOGLE_OAUTH_IN_ELECTRON — *INVOLVED* — custom-protocol
- WORKAROUND_FOR_GOOGLE_OAUTH_IN_ELECTRON — *INVOLVED* — one-time-cookie-bridge
- WORKAROUND_FOR_GOOGLE_OAUTH_IN_ELECTRON — *INTERCEPTED_NAVIGATION_TO* — accounts.google.com
- INTERCEPTED_NAVIGATION_TO_ACCOUNTS.GOOGLE.COM — *VIA* — webContents will-navigate
- WORKAROUND_FOR_GOOGLE_OAUTH_IN_ELECTRON — *USED* — shell.openExternal
- WORKAROUND_FOR_GOOGLE_OAUTH_IN_ELECTRON — *HANDED_SESSION_VIA* — custom URI scheme
- Google Cloud OAuth consent-screen — *HAD* — configuration issue
- configuration issue — *REQUIRED* — GCP Console
- Vinnstack desktop app — *REMOVED* — OAuth requirement
- OAuth requirement — *INCLUDED* — NextAuth
- OAuth requirement — *INCLUDED* — middleware gating
- OAuth requirement — *INCLUDED* — Electron OAuth bridge
- typed email — *IS_SUFFICIENT_IDENTITY_FOR* — single-operator-per-machine
- login/identity system — *KEYS* — per-user database row
- full OAuth flow — *IS_OVERKILL_FOR* — one person per machine usage pattern
- simple typed identifier — *IS_ALTERNATIVE_TO* — full OAuth flow
- simple typed identifier — *HAS_NO_DEPENDENCY_ON* — third party's admin console

%% ai-graph-end %%