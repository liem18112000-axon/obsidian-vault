---
title: "Vinnstack provider abstraction enables pluggable auth without UI/route changes"
created: 2026-07-02
type: model
status: evergreen
source: "Polaris integration research"
tags: [vinnstack, architecture, auth, provider-pattern]
---

# Vinnstack provider abstraction enables pluggable auth without UI/route changes

Vinnstack uses a registry-based provider abstraction that decouples authentication from the rest of the application. Each provider (Claude, Google Cloud, Bitbucket, Atlassian, etc.) implements a common interface: status(), login(), logout(), and optional setCredentials(). New providers are added by defining a provider object and registering it in a REGISTRY map—no changes to routes or UI needed. The UI component (Onboarding.tsx) automatically discovers and renders connection cards for all registered providers. This pattern supports both browser OAuth flows (where the CLI or browser handles sign-in) and typed-credential entry (where users enter credentials that are verified against an API endpoint before storage).
