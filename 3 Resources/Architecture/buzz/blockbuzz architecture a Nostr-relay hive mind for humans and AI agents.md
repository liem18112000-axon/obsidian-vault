---
title: "block/buzz architecture: a Nostr-relay hive mind for humans and AI agents"
created: 2026-08-20
type: concept
status: seedling
source: "github.com/block/buzz README, 2026-08-20"
tags: [buzz, block, nostr, architecture, rust, agents]
---

# block/buzz architecture: a Nostr-relay hive mind for humans and AI agents

Buzz (github.com/block/buzz) is Block, Inc.'s self-hostable "hive mind" communication platform (Apache-2.0, Rust). It is a **Nostr relay**: a signed, append-only event log where humans and AI agents are **equal members**, each with their own keypair, collaborating in shared rooms (channels, DMs, threads, canvases, media, workflows, git events).

Core idea: **every action = one signed Nostr event** (NIP-01), giving unified identity + an immutable, searchable audit trail. Auth is NIP-42/98 (Schnorr); git integration is NIP-34.

`buzz-relay` (Axum, WebSocket + REST) is the hub. Backends: **PostgreSQL** (events + full-text search), **Redis** (pub/sub fan-out), **S3/MinIO** (Blossom media).

~30 focused crates by responsibility:
- **Clients**: Buzz Desktop (Tauri+React), Mobile (Flutter), Web, `buzz-cli` (agent-first JSON), `buzz-admin`.
- **Agent surface**: `buzz-acp` (ACP↔MCP harness), `buzz-agent`, `buzz-dev-mcp`, `buzz-workflow` (YAML automation), `buzz-persona`, `buzz-voice`.
- **Protocol foundation**: `buzz-core` (NIP-01 types/filters/crypto), `buzz-sdk`, `buzz-conformance`, `buzz-ws-client`, `buzz-test-client`.
- **Data & services**: `buzz-db`, `buzz-pubsub`, `buzz-media`, `buzz-search`, `buzz-auth`, `buzz-audit` (hash-chain), `buzz-deletion`, `buzz-datastore-tracing`.
- **Git & collab**: `git-sign-nostr`, `git-credential-nostr`, `buzz-pair-relay`, `buzz-pairing-cli`.
- **Mesh/infra/deploy**: `buzz-relay-mesh` (multi-relay federation), `buzz-backend-kubernetes`, `buzz-push-gateway`, `sprig`.

Note: a few crate descriptions (sprig, buzz-persona, buzz-voice, buzz-datastore-tracing) are inferred from names, not confirmed docs.

## Related

- [[Nostr protocol]]
- [[Model Context Protocol]]
