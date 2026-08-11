---
ai_hash: 44ac0a0a1b0f0764
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: luz-docs test debugging, 2026-06-10
status: seedling
tags:
- wsl
- windows
- ports
- arquillian
- wildfly
title: WSL relay processes squat common dev ports on Windows
type: gotcha
---

# WSL relay processes squat common dev ports on Windows

On Windows with WSL2/Docker Desktop, `wslrelay` and `host-switch` listen on localhost ports that dev tools assume are free — observed squatting 8787 (the conventional JDWP debug port) and 9990 (WildFly management). The bind failure surfaces as confusing tool-specific errors (e.g. Arquillian: "JDWP Transport dt_socket failed to initialize" → "process exited with code 2").

Fix pattern: make the port configurable instead of fighting the relay. JBoss-style config files resolve `${sys.prop:default}` expressions, so `address=${wildfly.debug.port:8787}` in arquillian.xml keeps the old default but lets a local run pass `-Dwildfly.debug.port=18787`. Check the squatter with `Get-NetTCPConnection -LocalPort 8787 -State Listen`.

Related: [[Arquillian managed WildFly needs startupTimeoutInSeconds raised on slow machines]]

## Related

- [[Arquillian managed WildFly needs startupTimeoutInSeconds raised on slow machines]]

%% ai-graph-start %%

**Related notes:**
- [[Arquillian managed WildFly needs startupTimeoutInSeconds raised on slow machines]]
- [[Local luz-docs and luz_docs_statistic both bind host ports 8787 and 9990]]

%% ai-graph-end %%