---
title: "DocumentFile.java - Three-Repo Class Map"
created: 2026-07-07
type: observation
status: seedling
source: "session 2026-07-07"
tags: [java, code-graph, luz_docs, luz_enrichment, dependency-analysis]
---

# DocumentFile.java - Three-Repo Class Map

## Overview

The filename `DocumentFile.java` is shared across three unrelated packages in three repos. Asking about its dependencies requires specifying which repo.

## Key points

- Graphify code graph is the source of truth when local source clones are absent.
- All three variants are **leaf model classes** — minimal outgoing dependencies (primitives, `Serializable`, `Link`, Lombok/Jackson), but heavy *inbound* usage.
- When source clones are unavailable, Graphify graph edges (from `explain` mode) are preferred over BFS `query` output, which adds transitive noise.

## Three Variants

| Repo | Package | Role | Inbound degree |
|------|---------|------|---------------:|
| `luz_docs` | `ch.klara.luz.docs.model` | Core domain model | 27 |
| `luz_docs_view_controller` | `...resource.client.model` | Client-side DTO | 11 |
| `luz_enrichment` | `...command.model.email` | EML email attachment model | 9 |

### luz_docs (primary)
**Outgoing deps:** `Serializable`, `Link`, Lombok annotations, JDK primitives (`String`, `Long`).
**Inbound:** `Document`, `EmailInfo`, enrichers (`TimeStampEnricher`, `DocumentDiscoverEnricher`, `ThumbnailEnricher`), `EmlExtractorService`, `JsonEnricherUtil`, helper methods, integration tests.

### luz_docs_view_controller
**Outgoing deps:** `Serializable`, `Link`, Lombok/Jackson (`@Getter`, `@Setter`, `@ToString`, `@JsonIgnoreProperties`).
**Inbound:** `DocumentResponse`, `LetterBuilderService`.

### luz_enrichment
**Outgoing deps:** `Link`, `String`, `Integer`.
**Inbound:** `DefaultEmlExtractorService`, `EmailInfo`, `addToDocumentFileMap()`.

## Open questions

- The luz_docs graph view truncated 7 outgoing edges — fetch via Bitbucket read tools if the full field-type list is needed.
