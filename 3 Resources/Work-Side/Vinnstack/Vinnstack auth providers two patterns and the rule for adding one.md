---
title: "Vinnstack auth providers: two patterns and the rule for adding one"
created: 2026-07-02
type: lesson
status: seedling
source: "session 2026-07-02 — Polaris integration"
tags: [vinnstack, auth, architecture, design-decision, provider-pattern, implementation]
aliases:
  - Auth provider implementation checklist
  - How to add a new auth provider to Vinnstack
---

# Vinnstack auth providers: two patterns and the rule for adding one

Vinnstack's auth-provider layer (`lib/authProviders.ts`) grew from two ways a provider can authenticate to three; picking the right pattern is the first decision when adding a provider. Every provider is an object implementing the `AuthProvider` interface — `status()`, `login()`, `logout()`, optionally `setCredentials()` — registered in the `REGISTRY` map.

**Pattern 1 — CLI-driven action** (`browserLogin: true`, which just makes the card show a Sign in / Log out button). `login()`/`logout()` spawn a local vendor CLI — usually a browser OAuth flow (`claude auth login`, `gcloud auth login`). Requires the vendor's CLI to be installed. Used by: claude, google-cloud.

**Pattern 2 — credential entry** (`credentialFields` — each `{label, key, placeholder}` — plus `setCredentials()`). The operator types a token/credential into the card; `setCredentials()` verifies it against the vendor API (e.g. HTTP Basic) and persists it owner-only in `~/.agentic-os/config.json` via `saveConfig` (never returned to the browser — only presence flags). Used by: bitbucket, atlassian, vertex.

**Pattern 3 — status-only card** (`statusOnly: true`; added for Polaris). Read-only: `status()` reports connection state but the UI renders no action button, because setup isn't operator-driven from Vinnstack. Needed a small additive UI change — the button condition became `(loggedIn || browserLogin) && !statusOnly` — chosen over changing the shared condition, because the credential providers rely on the bare `loggedIn` clause to show their Log out button (touching it would regress them). `login`/`logout` stay as informational no-ops to satisfy the interface. Used by: polaris.

**Rule:** if the vendor ships a CLI, drive it (Pattern 1). Only reach for browser OAuth when a real CLI/OAuth-client exists — the codebase has *no* OAuth-callback infrastructure, so a token-only vendor uses credential entry (Pattern 2) instead. A provider can also reuse another provider's auth: Polaris needs no credentials of its own because its auth *is* Google Cloud ADC, already established by the google-cloud card.

**Adding a provider is mechanical once the pattern is chosen** — `components/Onboarding.tsx` auto-discovers and renders a card for anything in `REGISTRY`, and `app/api/auth/route.ts` + `app/api/auth/[provider]/route.ts` are generic across all providers. The whole checklist:

1. Extend the `ProviderId` union and set the provider's `ProviderKind` (`llm`, `cloud`, `scm`, `agents`, …).
2. Pattern 2 only: add config keys — `StoredConfig` field + `getConfig` resolution + a `publicConfig` presence flag.
3. Define the provider object (`status`/`login`/`logout`, plus `credentialFields`+`setCredentials` or `statusOnly`).
4. Add it to `REGISTRY` in `lib/authProviders.ts`. Nothing else to wire.

Example — Polaris (ePost): the research doc *assumed* an ePost document mailbox needing OAuth tokens, so a first pass built a credential-entry provider. The actual `polaris-cli` source showed Polaris is a **CLI wiring cloud-hosted AI agents/skills into editors over an MCP tunnel**, authed by Google Cloud ADC — no token at all. Rebuilt as **status-only** (`kind: "agents"`, `statusOnly: true`) where "connected" = installed (binary on `~/.polaris/bin` or PATH); tunnel state (TCP probe on `POLARIS_MCP_LOCAL_PORT`, default 3003) and Google-ADC presence show as detail. Gating on "bootstrapped" was abandoned — [[polaris-cli never writes ~.polarisstate.json — no reliable bootstrapped signal]]. **Lesson: verify a research doc's premise against the real source before implementing.**
