---
ai_hash: 3acf40229e046d82
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-13
entities: []
source: session 2026-06-13 accesstrade_integration
status: seedling
tags:
- docker
- rancher-desktop
- networking
- host-docker-internal
- gotcha
- ollama
title: 'Rancher Desktop: don''t pin host.docker.internal to host-gateway'
type: lesson
---

# Rancher Desktop: don't pin host.docker.internal to host-gateway

On **Rancher Desktop** (WSL2 backend), do NOT add `extra_hosts: ["host.docker.internal:host-gateway"]` to a compose service. Rancher already injects a working `host.docker.internal` that resolves to the real host (e.g. `192.168.127.254`) and can reach host services. The `host-gateway` keyword instead maps it to the container's WSL **bridge gateway** (`172.17.0.1`), where the host's service is NOT listening — so calls fail with **Connection refused**, not a timeout.

**Symptom:** a container can't reach a service on the host (e.g. Ollama on `:11434`) even though the host has it bound to `0.0.0.0`; inside the container `host.docker.internal` resolves to `172.17.0.1` and connection is refused.

**Why it bites:** `host-gateway` is the correct, often-required value on **native Linux Docker** (where `host.docker.internal` otherwise doesn't resolve), and it's harmless on **Docker Desktop**. So a compose file written for Linux/Desktop silently breaks only under Rancher Desktop. Fix: drop the `extra_hosts` override (Desktop + Rancher both provide host.docker.internal natively) and re-add it only for native-Linux deploys.

**Tell engines apart:** `docker info --format '{{.OperatingSystem}}'` → `Rancher Desktop WSL Distribution` vs `Docker Desktop`.

Found while a Dockerized FastAPI app couldn't reach the host's Ollama for an LLM feature. Related: [[3 Resources/Infra/Docker/Docker Compose path resolution env_file vs build context vs dockerfile]], [[Relocating docker-compose.yml renames the Compose project and orphans volumes]].

## Related

- [[3 Resources/Infra/Docker/Docker Compose path resolution env_file vs build context vs dockerfile]]
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]

%% ai-graph-start %%

**Related notes:**
- [[Docker hostname for reaching a service depends on where the caller runs]]
- [[Docker Compose path resolution env_file vs build context vs dockerfile]]
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]
- [[Separate docker-compose files are isolated networks; use one file + a profile for optional services]]

%% ai-graph-end %%