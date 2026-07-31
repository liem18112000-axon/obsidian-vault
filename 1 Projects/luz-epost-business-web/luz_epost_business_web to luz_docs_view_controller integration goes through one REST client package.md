---
ai_hash: 6b2e9188f6e33e8e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-08
entities:
- luz_epost_business_web
- luz_docs_view_controller
- integration
- REST client package
- ch.klara.luz.epost.business.letterbox.repository.rest
- AxonIvy business web app
- Jakarta EE REST service
- CommunicableToLuzDocsViewControllerRestResource.java
- RestClient
- RestResource.LUZ_DOCS_VIEW_CONTROLLER.newClient()
- LetterRestClient
- LetterDocumentRestClient
- TrashRestClient
- SmartLetterRestClient
- AuditLogRestClient
- SecurityClassRestClient
- folder-specific clients
- LetterResource.java
- REST resources
- Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern
- JSON adapters
- LocalDate
- LocalDateTime
- Instant
- Double
- audit
source: session 2026-07-08
status: seedling
tags:
- luz
- luz-epost-business-web
- luz-docs-view-controller
- rest-api
- architecture
title: luz_epost_business_web to luz_docs_view_controller integration goes through
  one REST client package
type: observation
---

# luz_epost_business_web to luz_docs_view_controller integration goes through one REST client package

The AxonIvy business web app `luz_epost_business_web` (caller) talks to the Jakarta EE REST service `luz_docs_view_controller` (callee) through one narrow integration point: the `ch.klara.luz.epost.business.letterbox.repository.rest` package.

Locally: caller at `C:\Users\dvtliem\Kepler\luz_epost_business_web`, callee at `C:\Users\dvtliem\Kepler\luz_docs_view_controller`.

The base interface `CommunicableToLuzDocsViewControllerRestResource.java` builds the shared RestClient (tenant path, auth token, language params, JSON adapters for LocalDate/LocalDateTime/Instant/Double) via `RestResource.LUZ_DOCS_VIEW_CONTROLLER.newClient()`. ~15 concrete REST client classes implement it — `LetterRestClient` (largest, ~27KB, the main letter CRUD/history/storage-modification surface), `LetterDocumentRestClient`, `TrashRestClient`, `SmartLetterRestClient`, `AuditLogRestClient`, `SecurityClassRestClient`, plus a `storage/` subpackage of folder-specific clients.

On the callee side, the matching REST resources live in `luz_docs_view_controller/src/main/java/ch/klara/luz/docs/view/controller/resource/` — e.g. `LetterResource.java` is the counterpart to `LetterRestClient.java`.

Useful starting point for any future audit of this integration: read `CommunicableToLuzDocsViewControllerRestResource.java` first to understand the shared client setup, then diff each `*RestClient.java` against its matching `*Resource.java` on the callee side.

Related: [[Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern]]

%% ai-graph-start %%

**Related notes:**
- [[Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern]]
- [[DocumentFile.java - Three-Repo Class Map]]
- [[luz-docs API request bodies are only observable as downstream luz-jsonstore queries]]
- [[01 Overview]]
- [[Real luz_docsluz_jsonstore source lives under Kepler, not epost_knowledge_base]]

**Relations:**
- luz_epost_business_web — *is_part_of* — integration
- luz_docs_view_controller — *is_part_of* — integration
- luz_epost_business_web — *is_a* — AxonIvy business web app
- luz_docs_view_controller — *is_a* — Jakarta EE REST service
- integration — *goes_through* — REST client package
- REST client package — *is_named* — ch.klara.luz.epost.business.letterbox.repository.rest
- luz_epost_business_web — *is_caller_for* — luz_docs_view_controller
- luz_docs_view_controller — *is_callee_for* — luz_epost_business_web
- CommunicableToLuzDocsViewControllerRestResource.java — *builds* — RestClient
- RestClient — *is_created_via* — RestResource.LUZ_DOCS_VIEW_CONTROLLER.newClient()
- CommunicableToLuzDocsViewControllerRestResource.java — *is_base_interface_for* — LetterRestClient
- LetterRestClient — *implements* — CommunicableToLuzDocsViewControllerRestResource.java
- LetterDocumentRestClient — *implements* — CommunicableToLuzDocsViewControllerRestResource.java
- TrashRestClient — *implements* — CommunicableToLuzDocsViewControllerRestResource.java
- SmartLetterRestClient — *implements* — CommunicableToLuzDocsViewControllerRestResource.java
- AuditLogRestClient — *implements* — CommunicableToLuzDocsViewControllerRestResource.java
- SecurityClassRestClient — *implements* — CommunicableToLuzDocsViewControllerRestResource.java
- folder-specific clients — *are_part_of* — REST client package
- LetterResource.java — *is_counterpart_to* — LetterRestClient
- luz_docs_view_controller — *contains* — REST resources
- LetterResource.java — *is_a* — REST resources
- CommunicableToLuzDocsViewControllerRestResource.java — *is_useful_starting_point_for* — audit
- audit — *involves_diffing* — LetterRestClient
- audit — *involves_diffing* — LetterResource.java
- RestClient — *uses* — JSON adapters
- JSON adapters — *for* — LocalDate
- JSON adapters — *for* — LocalDateTime
- JSON adapters — *for* — Instant
- JSON adapters — *for* — Double
- Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern — *is_related_to* — integration

%% ai-graph-end %%