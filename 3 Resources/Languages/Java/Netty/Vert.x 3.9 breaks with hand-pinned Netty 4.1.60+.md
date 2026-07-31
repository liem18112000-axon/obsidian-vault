---
ai_hash: 3b2dbe3bd52eb499
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: LEO CDP migration planning, session 2026-06-06
status: seedling
tags:
- vertx
- netty
- gotcha
- cve
- leo-cdp
title: Vert.x 3.9 breaks with hand-pinned Netty 4.1.60+
type: lesson
---

# Vert.x 3.9 breaks with hand-pinned Netty 4.1.60+

Vert.x 3.9.x is incompatible with hand-pinned Netty 4.1.60+ — the header-validation changes Netty made for CVE-2021-21295 cause `ClassCastException` in Vert.x 3.9's HTTP header processing (eclipse-vertx/vert.x#3865).

Lesson: when bumping a framework that embeds Netty, use the Netty version the framework itself declares; do not force-pin Netty to 'latest 4.1.x' for CVE hygiene without a full HTTP/WebSocket regression. If a Netty CVE matters, prefer bumping the framework patch line (e.g. Vert.x 3.9.16, the final 3.x, ships a much newer aligned Netty) over overriding the transitive.

## Related

- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]

%% ai-graph-start %%

**Related notes:**
- [[Netty 4.1 on JDK 24+ needs --sun-misc-unsafe-memory-access=allow]]
- [[Vert.x 3.8.5 plus Netty 4.1.44 run on JDK 25 with flags only - LEO CDP empirical result]]
- [[JDK 25 perf parity must be load-tested - clean boot hid a 15 percent Netty regression]]
- [[Decouple runtime JDK from bytecode target when migrating Java versions]]
- [[Binary-only vendored jars pin the API level of everything they link]]

%% ai-graph-end %%