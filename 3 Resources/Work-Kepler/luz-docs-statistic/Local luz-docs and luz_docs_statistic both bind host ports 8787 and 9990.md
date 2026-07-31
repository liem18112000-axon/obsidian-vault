---
title: "Local luz-docs and luz_docs_statistic both bind host ports 8787 and 9990"
created: 2026-06-11
type: lesson
status: seedling
source: "session 2026-06-11"
tags: [luz-docs, luz-docs-statistic, docker-compose, gotcha]
---

# Local luz-docs and luz_docs_statistic both bind host ports 8787 and 9990

The default docker-compose files of luz_docs (`luz_docs-luz-docs-1`) and luz_docs_statistic both publish host ports **8787** (JVM debug) and **9990** (WildFly management), so the two services cannot run locally at the same time with stock compose files — the second one fails with `Bind for 0.0.0.0:8787 failed: port is already allocated`, leaving a half-created container behind (`docker rm -f` it before retrying).

Workarounds: stop the other container, remap debug/management ports for one of them, or drop those port mappings entirely (the app ports differ already: luz-docs on 8147, luz-docs-statistic on 8199, so only debug/management clash).

## Related
- [[Run luz_docs_statistic locally with docker-compose]]
