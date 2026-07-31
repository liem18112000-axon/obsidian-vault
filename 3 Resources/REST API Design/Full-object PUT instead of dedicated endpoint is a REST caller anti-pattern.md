---
title: "Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern"
created: 2026-07-08
type: lesson
status: seedling
source: "session 2026-07-08, luz-epost-business-web to luz-docs-view-controller audit"
tags: [rest-api, code-review, luz, anti-pattern]
---

# Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern

When reviewing a REST caller, check whether it PUTs/serializes the entire parent object to change one field, when the server already exposes a dedicated lightweight endpoint for that exact field change.

Concrete instance: in luz_epost_business_web, `LetterModifyingInteractor.assignSecurityClass`/`unassignSecurityClass` call `letterRepository.update(letter)` — a full-object `PUT /{tenant}/letters/{id}` serializing the entire Letter (metadata, tags, folder refs, history) — even though luz_docs_view_controller's `LetterResource` already exposes `POST /letters/{id}/security-class/{securityClassId}`, a purpose-built endpoint that takes only the id. The lightweight endpoint existed and was simply unused by the caller.

Same family of anti-pattern, different shape: a caller that needs to do the same lightweight operation N times (e.g. undo N single-letter storage modifications) falls back to N sequential single-item calls even though a batch endpoint for that exact operation already exists and is already used on the *forward* path (`createStorageModifications(Map<letterId, mod>)` → `POST /letters/storage-modifications`). The tell is: forward path is batched, reverse/undo path isn't.

Why this happens: usually because the client-side wrapper library exposes a generic `update(entity)` / per-item method as the path of least resistance, and nobody wired the specific-purpose or batch endpoint into the caller when it was added server-side. It's cheap to fix (repoint the caller) once found, but easy to miss without deliberately diffing 'what lightweight/batch endpoints exist server-side' against 'what the caller actually calls.'

How to find it: for a given caller class (RestClient/repository), list its outbound calls, then grep the server-side resource class for sibling @Path variants (singular vs plural id in the path, or a narrower id-only path under the same resource) that the caller isn't using.

Related: [[luz-docs folder delete filter double-fetched every subfolder]] and [[luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder]] are the same 'redundant call' failure mode in a different luz service (Mongo N+1 instead of missed lightweight/batch REST endpoint).
