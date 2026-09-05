---
title: "A remote A2A agent needs its own connectors because MCP is client-side"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — test-agent proposal"
tags: [a2a, mcp, architecture, agentic]
---

# A remote A2A agent needs its own connectors because MCP is client-side

When a **remote agent runs unattended on a server (e.g. GCP Cloud Run)** and is orchestrated by **local Claude over the Agent2Agent (A2A) protocol**, the remote agent must ship its **own minimal API connector** to any external system (Jira, Confluence, …) — it cannot borrow Claude's MCP servers.

**Why:** MCP servers are **client-side** — they live next to the Claude client on the developer's machine. An A2A *remote* agent is a different process on a different host with no access to them. So "the agent can already reach Atlassian via MCP" is false for the remote half. Give the remote agent a narrow, declared, **read-only** REST client instead (a handful of GET endpoints, one secret from Secret Manager, zero write scopes) — which also satisfies the "connectors declared as an explicit allowlist" governance rule.

**Corollary — memory split under A2A:**
- **Short-term memory is free** in A2A: task history + `contextId` group related messages/tasks for you.
- **Long-term memory is yours to build** — it does not come with the protocol. Here: a GCS bucket of markdown notes + a link-graph index + run-logs, deliberately *curated facts, not a transcript*.

Context: LUZ-159671 Testing-Agent first slice.

## Related

- [[Knowledge-Gathering loop is a bounded frontier crawl with a verify edge]]
