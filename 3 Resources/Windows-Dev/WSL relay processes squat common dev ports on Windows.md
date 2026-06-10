---
title: "WSL relay processes squat common dev ports on Windows"
created: 2026-06-10
type: gotcha
status: seedling
source: "luz-docs test debugging, 2026-06-10"
tags: [wsl, windows, ports, arquillian, wildfly]
---

# WSL relay processes squat common dev ports on Windows

On Windows with WSL2/Docker Desktop, `wslrelay` and `host-switch` listen on localhost ports that dev tools assume are free — observed squatting 8787 (the conventional JDWP debug port) and 9990 (WildFly management). The bind failure surfaces as confusing tool-specific errors (e.g. Arquillian: "JDWP Transport dt_socket failed to initialize" → "process exited with code 2").

Fix pattern: make the port configurable instead of fighting the relay. JBoss-style config files resolve `${sys.prop:default}` expressions, so `address=${wildfly.debug.port:8787}` in arquillian.xml keeps the old default but lets a local run pass `-Dwildfly.debug.port=18787`. Check the squatter with `Get-NetTCPConnection -LocalPort 8787 -State Listen`.

Related: [[Arquillian managed WildFly needs startupTimeoutInSeconds raised on slow machines]]

## Related

- [[Arquillian managed WildFly needs startupTimeoutInSeconds raised on slow machines]]
