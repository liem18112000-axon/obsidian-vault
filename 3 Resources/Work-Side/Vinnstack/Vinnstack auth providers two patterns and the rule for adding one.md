---
title: "Vinnstack auth providers: two patterns and the rule for adding one"
created: 2026-07-02
type: lesson
status: seedling
source: "session 2026-07-02 — Polaris integration"
tags: [vinnstack, auth, architecture, design-decision]
---

# Vinnstack auth providers: two patterns and the rule for adding one

Vinnstack's auth-provider layer (`lib/authProviders.ts`) supports exactly two ways a provider can authenticate, and picking the right one is the first decision when adding a provider.

**Pattern 1 — CLI-driven action** (`browserLogin: true`, which just makes the card show a Sign in / Log out button). `login()`/`logout()` spawn a local vendor CLI — usually a browser OAuth flow (`claude auth login`, `gcloud auth login`). Requires the vendor's CLI to be installed. Used by: claude, google-cloud.

**Pattern 2 — credential entry** (`credentialFields` + `setCredentials()`). The operator types a token/credential into the card; it's verified against the vendor API and persisted owner-only in `~/.agentic-os/config.json` via `saveConfig` (never returned to the browser — only presence flags). Used by: bitbucket, atlassian, vertex.

**Pattern 3 — status-only card** (`statusOnly: true`; added for Polaris). Read-only: `status()` reports connection state but the UI renders no action button, because setup isn't operator-driven from Vinnstack. Needed a small additive UI change — the button condition became `(loggedIn || browserLogin) && !statusOnly` — chosen over changing the shared condition, because the credential providers rely on the bare `loggedIn` clause to show their Log out button (touching it would regress them). `login`/`logout` stay as informational no-ops to satisfy the interface. Used by: polaris.

**Rule:** if the vendor ships a CLI, drive it (Pattern 1). Only reach for browser OAuth when a real CLI/OAuth-client exists — the codebase has *no* OAuth-callback infrastructure, so a token-only vendor uses credential entry (Pattern 2) instead. A provider can also reuse another provider's auth: Polaris needs no credentials of its own because its auth *is* Google Cloud ADC, already established by the google-cloud card.

**Adding a provider is mechanical once the pattern is chosen:** the UI (`components/Onboarding.tsx`) and the API routes (`/api/auth`, `/api/auth/[provider]`) auto-render and auto-handle anything in the `REGISTRY`. So the work is: extend `ProviderId`/`ProviderKind`, (for Pattern 2) add config keys (StoredConfig + getConfig resolution + publicConfig presence flag), define the provider object, register it.

Example — Polaris (ePost): the research doc *assumed* it was an ePost document mailbox needing OAuth/bearer tokens, so a first pass built it as a credential-entry provider. Reading the actual `polaris-cli` source revealed Polaris is really a **CLI that wires cloud-hosted AI agents/skills into editors over an MCP tunnel**, authed by Google Cloud ADC — no token at all. It was rebuilt as a **status-only** provider (`kind: "agents"`, `statusOnly: true`): "connected" = **installed** (binary on `~/.polaris/bin` or PATH). Gating on "bootstrapped" was attempted but abandoned — the CLI never writes `~/.polaris/state.json`, so that signal is unreliable (see [[polaris-cli never writes ~.polarisstate.json — no reliable bootstrapped signal]]). Tunnel state (TCP probe on port 3003, default `POLARIS_MCP_LOCAL_PORT`) and Google-ADC presence show as detail. **Lesson: verify a research doc's premise against the real source before implementing — the doc can be confidently wrong about what a system even is.**
