---
ai_hash: 3b6062b56d639b57
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11
status: seedling
tags:
- luz-docs
- luz-docs-statistic
- docker-compose
- gotcha
title: Local luz-docs and luz_docs_statistic both bind host ports 8787 and 9990
type: lesson
---

# Local luz-docs and luz_docs_statistic both bind host ports 8787 and 9990

The default docker-compose files of luz_docs (`luz_docs-luz-docs-1`) and luz_docs_statistic both publish host ports **8787** (JVM debug) and **9990** (WildFly management), so the two services cannot run locally at the same time with stock compose files — the second one fails with `Bind for 0.0.0.0:8787 failed: port is already allocated`, leaving a half-created container behind (`docker rm -f` it before retrying).

Workarounds: stop the other container, remap debug/management ports for one of them, or drop those port mappings entirely (the app ports differ already: luz-docs on 8147, luz-docs-statistic on 8199, so only debug/management clash).

## Related
- [[Run luz_docs_statistic locally with docker-compose]]

%% ai-graph-start %%

**Related notes:**
- [[Run luz_docs_statistic locally with docker-compose]]
- [[luz_online_payment local Docker run pattern (WildFly WAR + GAR base + local Postgres)]]
- [[WSL relay processes squat common dev ports on Windows]]

%% ai-graph-end %%