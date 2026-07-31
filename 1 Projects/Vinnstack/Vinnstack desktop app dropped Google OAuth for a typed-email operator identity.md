---
title: "Vinnstack desktop app dropped Google OAuth for a typed-email operator identity"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack desktop-packaging session 2026-07-07"
tags: [vinnstack, electron, oauth, architecture-decision]
---

# Vinnstack desktop app dropped Google OAuth for a typed-email operator identity

In the Vinnstack desktop-app project, the Google OAuth login gate (NextAuth-based "Sign in with Google" whole-app gate, used to key a per-account Claude relay token) was removed and replaced with a plain email text field captured once during onboarding. The email becomes the account_credentials table key directly (no session, no login flow at all).

Why: getting Google OAuth working inside an Electron BrowserWindow required a real workaround (Google blocks embedded browsers via its disallowed_useragent policy), which was solved with a system-browser-redirect + custom-protocol + one-time-cookie-bridge mechanism (see [[Electron OAuth needs a system-browser handoff, not an embedded window]] if that note exists, or the reasoning: intercept navigation to accounts.google.com via webContents will-navigate, shell.openExternal it, then hand the resulting session back into Electron via a registered custom URI scheme). That mechanism worked, but then a live end-to-end test hit an unrelated Google Cloud OAuth consent-screen configuration issue ("Try signing in with a different account", rejected mid-consent for an account that had worked before in a browser) that could not be diagnosed or fixed without GCP Console access. Rather than debug a third-party admin/consent-screen policy blocking progress, the pragmatic call was to drop the OAuth requirement entirely for this desktop app: it is single-operator-per-machine already, so a typed email is sufficient identity, and the whole login mechanism (NextAuth, middleware gating, the Electron OAuth bridge) was dead weight for that use case.

Lesson: when a login/identity system exists only to key a per-user database row (not for real authorization/security), and the actual usage pattern is "one person per machine," a full OAuth flow is often overkill — a simple typed identifier captured once in onboarding does the same job with far less moving parts and no dependency on a third party’s admin console being configured correctly.
