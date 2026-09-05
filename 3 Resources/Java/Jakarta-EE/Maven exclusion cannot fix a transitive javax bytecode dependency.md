---
title: "Maven exclusion cannot fix a transitive javax bytecode dependency"
created: 2026-08-16
aliases: ["javax exclusion NoClassDefFoundError"]
type: lesson
status: seedling
source: "session 2026-08-16 luz_docs_import Java21 migration planning"
tags: [jakarta-ee, maven, javax, wildfly, migration, gotcha]
---

# Maven exclusion cannot fix a transitive javax bytecode dependency

A Maven `<exclusion>` only removes a jar from the classpath **edge** of the dependency you declare — it does **not** rewrite the *bytecode* of that dependency. If a library was compiled against `javax:javaee-api` (Jakarta EE 7/8), its own `.class` files carry hard references to `javax.*` types. Excluding `javax:javaee-api` from it just means those types are now **missing** at runtime, so on a Jakarta EE 10 server (e.g. WildFly 33, where the platform ships `jakarta.*` only) you get `NoClassDefFoundError: javax/...` the moment that code path runs.

**Why it bites:** the exclusion looks like it 'cleaned up' the javax dependency, and compilation of *your* module may even succeed (your code never touches the excluded API). The failure is deferred to class-loading at runtime, inside the third-party jar.

**Real fixes (pick one):**
- **Upstream jakarta build** — the library owner recompiles against `jakarta.jakartaee-api` and republishes. Cleanest; blocks on someone else.
- **Eclipse Transformer** — run the *compiled* jar through the Eclipse Transformer (`org.eclipse.transformer`) at build time; it rewrites `javax.*` → `jakarta.*` in the bytecode and package names, producing a jakarta-namespace jar you consume. Self-service, but fragile for EJB-packaged / reflection-heavy jars.

**Rule of thumb:** an `<exclusion>` can resolve a *version/duplicate* conflict, never a *namespace* migration. Namespace migration is a bytecode problem, and bytecode problems need recompilation or transformation.

Discovered while planning the `luz_docs_import` Java 17→21 / WildFly 26→33 (Jakarta EE 8→10) migration: internal deps `luz_common` and `luzsec_service` are still `javax` EE7 on master with no jakarta build, and the app's existing `<exclusion>` of `javax:javaee-api` from `luz_common` masks — but cannot fix — the underlying transitive javax bytecode.

## Related

- [[Jakarta EE 8 to 10 namespace migration]]
- [[Eclipse Transformer]]
