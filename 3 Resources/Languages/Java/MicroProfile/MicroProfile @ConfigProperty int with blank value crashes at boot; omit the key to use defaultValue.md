---
title: "MicroProfile @ConfigProperty int with blank value crashes at boot; omit the key to use defaultValue"
created: 2026-06-23
type: gotcha
status: seedling
source: "luz-docs LUZ-154613 session 2026-06-23"
tags: [microprofile, config, luz-docs, gotcha, wildfly]
---

# MicroProfile @ConfigProperty int with blank value crashes at boot; omit the key to use defaultValue

A MicroProfile `@ConfigProperty` injected into a **primitive int** (or other non-String type) throws a type-conversion error and **fails at boot/deploy** if the config value is present-but-empty (`KEY=` in system.properties). `defaultValue` only kicks in when the key is **absent entirely** — not when it is blank.

**Consequence:** to turn a numeric-tuned feature OFF in one env (e.g. prod), **omit or comment the key**, never set it blank. A blank value is only safe for String-backed or boolean-ish flags (which parse empty as falsey/no-op).

Example: luz-docs `LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS` (count fan-out K, `@ConfigProperty int`, default `1` = off). For prod we commented it (`#LUZ_DOCS_PARALLELIZE_COUNT_PARTITIONS=8`) so the key is absent → default 1 → fan-out disabled, instead of `=` which would crash WildFly on startup.

See also [[Renaming a config key silently disables the feature if old env var name stays in overlays]].

## Related

- [[Renaming a config key silently disables the feature if old env var name stays in overlays]]
