---
title: "Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern"
created: 2026-07-08
type: lesson
status: seedling
source: "session 2026-07-08, luz-epost-business-web → luz-docs-view-controller audit"
tags: [rest-api, code-review, luz, anti-pattern]
---

# Full-object PUT instead of dedicated endpoint is a REST caller anti-pattern

A caller that serializes and PUTs an entire parent object to change **one field**, while the server already exposes a purpose-built endpoint for exactly that change, is wasting payload, risking lost-update races on the untouched fields, and bypassing the server's narrow contract.

Instance: `LetterModifyingInteractor.assignSecurityClass` / `unassignSecurityClass` (luz_epost_business_web) call `letterRepository.update(letter)` → full-object `PUT /{tenant}/letters/{id}` (metadata, tags, folder refs, history) even though `LetterResource` already offers `POST /letters/{id}/security-class/{securityClassId}`, which takes only the id.

**Same family, batch shape:** the reverse/undo path issues N sequential single-item calls while the forward path already uses a batch endpoint — e.g. `createStorageModifications(Map<letterId, mod>)` → `POST /letters/storage-modifications`. The tell is the asymmetry: forward batched, undo not.

**Root cause:** the client wrapper exposes a generic `update(entity)` / per-item method as the path of least resistance, and nobody repointed the caller when the specific or batch endpoint shipped server-side.

**How to find it:** list a caller class's outbound calls, then grep the server resource for sibling `@Path` variants it isn't using (narrower id-only paths, plural/batch variants under the same resource). Cheap to fix, invisible without the deliberate diff.

## Related

- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder]]
