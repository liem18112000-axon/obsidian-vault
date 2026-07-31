---
title: "Auth provider implementation checklist: 4 steps to add a new provider to Vinnstack"
created: 2026-07-02
type: howto
status: evergreen
source: "Polaris integration research"
tags: [vinnstack, auth, provider-pattern, implementation]
---

# Auth provider implementation checklist: 4 steps to add a new provider to Vinnstack

To add a new auth provider to Vinnstack: (1) Update the ProviderId type union and define the provider's kind ('llm', 'cloud', 'scm', etc.). (2) Create a provider object implementing AuthProvider interface with status(), login(), logout(), and optionally setCredentials(). (3) Add the provider to the REGISTRY map in lib/authProviders.ts. That is sufficient—routes in app/api/auth/[provider]/route.ts are generic and handle all providers, and the UI component Onboarding.tsx automatically discovers and renders connection cards for all registered providers. For browser OAuth flows, follow the Claude or Google Cloud pattern: spawn a CLI command or redirect to OAuth authorize endpoint and wait for the browser flow to complete. For typed-credential providers (Bitbucket, Atlassian), define credentialFields with label/key/placeholder, implement setCredentials() to verify via API endpoint (e.g., HTTP Basic auth), then saveConfig() to persist locally.
