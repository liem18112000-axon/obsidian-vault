---
ai_hash: 94624d03bf21b9a1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-02
entities: []
source: Polaris integration research
status: evergreen
tags:
- vinnstack
- architecture
- auth
- provider-pattern
title: Vinnstack provider abstraction enables pluggable auth without UI/route changes
type: model
---

# Vinnstack provider abstraction enables pluggable auth without UI/route changes

Vinnstack uses a registry-based provider abstraction that decouples authentication from the rest of the application. Each provider (Claude, Google Cloud, Bitbucket, Atlassian, etc.) implements a common interface: status(), login(), logout(), and optional setCredentials(). New providers are added by defining a provider object and registering it in a REGISTRY map—no changes to routes or UI needed. The UI component (Onboarding.tsx) automatically discovers and renders connection cards for all registered providers. This pattern supports both browser OAuth flows (where the CLI or browser handles sign-in) and typed-credential entry (where users enter credentials that are verified against an API endpoint before storage).

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack auth providers two patterns and the rule for adding one]]
- [[Local-app provider sign-in drive the vendor CLI; Vertex is the exception (gcloud ADC + projectregion)]]
- [[Vinnstack desktop app dropped Google OAuth for a typed-email operator identity]]
- [[Local-app OAuth bridges the browser callback to the waiting login via an in-memory state-to-resolver map]]
- [[Per-account credential store should only hold per-identity secrets]]

%% ai-graph-end %%