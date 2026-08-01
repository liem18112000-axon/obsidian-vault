---
title: "Buzz (block/buzz) is a Nostr-based workspace where humans and AI agents are peers"
created: 2026-08-01
aliases: ["Buzz", "block/buzz"]
type: concept
status: seedling
source: "github.com/block/buzz research 2026-08-01"
tags: [nostr, buzz, block, ai-agents, collaboration, self-hosting]
---

# Buzz (block/buzz) is a Nostr-based workspace where humans and AI agents are peers

Buzz (github.com/block/buzz, by Block, Inc., Apache-2.0, Rust) is a self-hostable team-collaboration workspace built on the **Nostr** protocol whose defining bet is that **humans and AI agents are first-class peers**: every participant holds a secp256k1 keypair and Schnorr-signs every action, so messages, reactions, git patches, and workflow steps all live as signed events in **one append-only event log served by a relay you own**.

Why it matters / what to remember:
- **One event log, three lenses** — the same store is read as Stream (Slack-like realtime), Forum (Discourse-like async), and Workflow (structured YAML automation). No data duplication.
- **The relay is the single source of truth** — Axum server; no P2P/gossip/replication. Backed by Postgres (events + full-text search), Redis (pub/sub, presence, typing), S3/MinIO (Blossom media).
- **The URL is the community** — tenant isolation is enforced at the protocol/host level, not as a filter.
- **Agents connect via the `buzz-acp` harness** (ACP / JSON-RPC over stdio); named runtimes: Goose, OpenAI Codex, Claude Code. Agents get their own keys, channel memberships, and scoped permissions — auditable like humans.
- Protocol: Nostr NIP-01 (wire), NIP-42 (WS auth), NIP-98 (HTTP auth), NIP-34 (git), custom kinds 40000-49999. See [[Nostr custom kinds as a feature-extension mechanism]].
- Positioned by press as a **Nostr-based Slack + GitHub alternative for the AI-agent era**. Early-stage (v0.x): rate limiting is a stub, approval gates and some workflow actions are WIP, mobile (Flutter) unfinished.

Full deep-dive research (overview, architecture, crate map, getting-started, vision/roadmap, sources) exported to `C:\Users\dvtliem\.claude\docs\Buzz`.

## Related

- [[Nostr custom kinds as a feature-extension mechanism]]
