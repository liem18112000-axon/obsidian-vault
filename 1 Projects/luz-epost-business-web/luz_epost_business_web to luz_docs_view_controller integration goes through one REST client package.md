---
title: "luz_epost_business_web to luz_docs_view_controller integration goes through one REST client package"
created: 2026-07-08
type: observation
status: seedling
source: "session 2026-07-08"
tags: [luz, luz-epost-business-web, luz-docs-view-controller, rest-api, architecture]
---

# luz_epost_business_web to luz_docs_view_controller integration goes through one REST client package

The AxonIvy business web app `luz_epost_business_web` (caller) talks to the Jakarta EE REST service `luz_docs_view_controller` (callee) through one narrow integration point: the `ch.klara.luz.epost.business.letterbox.repository.rest` package.

Locally: caller at `C:\Users\dvtliem\Kepler\luz_epost_business_web`, callee at `C:\Users\dvtliem\Kepler\luz_docs_view_controller`.

The base interface `CommunicableToLuzDocsViewControllerRestResource.java` builds the shared RestClient (tenant path, auth token, language params, JSON adapters for LocalDate/LocalDateTime/Instant/Double) via `RestResource.LUZ_DOCS_VIEW_CONTROLLER.newClient()`. ~15 concrete REST client classes implement it — `LetterRestClient` (largest, ~27KB, the main letter CRUD/history/storage-modification surface), `LetterDocumentRestClient`, `TrashRestClient`, `SmartLetterRestClient`, `AuditLogRestClient`, `SecurityClassRestClient`, plus a `storage/` subpackage of folder-specific clients.

On the callee side, the matching REST resources live in `luz_docs_view_controller/src/main/java/ch/klara/luz/docs/view/controller/resource/` — e.g. `LetterResource.java` is the counterpart to `LetterRestClient.java`.

Useful starting point for any future audit of this integration: read `CommunicableToLuzDocsViewControllerRestResource.java` first to understand the shared client setup, then diff each `*RestClient.java` against its matching `*Resource.java` on the callee side.

Related: [[Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern]]
