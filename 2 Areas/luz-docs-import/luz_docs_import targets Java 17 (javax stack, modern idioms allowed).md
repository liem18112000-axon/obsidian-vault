---
title: "luz_docs_import targets Java 17 (javax stack, modern idioms allowed)"
created: 2026-08-04
type: lesson
status: seedling
source: "LUZ-158230 impl 2026-08-04"
tags: [luz-docs-import, java, kepler, gotcha]
---

# luz_docs_import targets Java 17 (javax stack, modern idioms allowed)

The luz_docs_import service targets **Java 17** (`pom.xml`: `maven.compiler.source/target = 17`), even though it runs on the Java EE `javax.*` stack (javax.ejb, javax.ws.rs, javax.json, MicroProfile REST clients on WildFly). So modern language idioms ARE available and are used in the codebase: `var`, records, `instanceof` pattern matching, `String.formatted()`, `List.of`, text blocks.

Correction: an earlier note claimed this repo was Java 8 — that was wrong (inferred before checking the pom). Always confirm the compiler target in pom.xml (`maven.compiler.target` / `maven.compiler.release`) rather than inferring the Java level from the `javax.*` imports — javax vs jakarta indicates the EE namespace, NOT the JDK language level.

JSON is javax.json (JsonObject/JsonObjectBuilder/JsonValue/JsonString), not Jackson/Gson. Models use Lombok (@Getter/@Setter/@Builder).

Related: [[luz_docs_import adds document metadata only in DocsImportAsyncService.createDocument()]]

## Related

- [[luz_docs_import adds document metadata only in DocsImportAsyncService.createDocument()]]
