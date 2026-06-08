---
title: "Verify a response-shape regression by tracing the downstream consumer, not the shape diff"
created: 2026-06-08
type: lesson
status: seedling
source: "luz_docs materialize review #15, 2026-06-08"
tags: [code-review, json-api, jackson, gotcha, luz-docs]
---

# Verify a response-shape regression by tracing the downstream consumer, not the shape diff

A "response-shape regression" finding (a producer service now returns fewer/narrower fields in a JSON response) is only a real regression if the downstream consumer **reads** a field that disappeared. Judge it by tracing the consumer, never by the shape diff alone.

## Procedure
1. Find the consumer's deserialization target class (the DTO the response is read into).
2. Check for `@JsonIgnoreProperties(ignoreUnknown=true)` (Jackson). If present, dropped/narrowed keys deserialize to `null` — **never a parse failure**. The risk collapses to: which fields are actually *read*.
3. Grep every getter call on that model across the consumer. A narrowed response is **harmless** when the producer's new shape is still a **superset** of the fields the consumer reads.

## Gotcha — same DTO, multiple paths
A model class is often reused for several endpoints. Only reads on the path through the **changed producer** count; reads off other paths (a different endpoint that fills the same DTO) are out of scope. Scope the grep to the right call path.

## Concrete instance
luz_docs materialize review finding #15: `MaterializeResponseBuilder.pair()` narrowed the doc-embedded `_folders[]` from full folder docs to `{_id, name, securityClassCodes}`. Hypothesis: breaks consumers reading `parentFolderIds`/`inheritedSecurityClassCodes`. Reality: the sole consumer of doc-embedded `_folders` (`luz_docs_view_controller` `LetterBuilderService.buildFoldersForNormalLetter`) reads only `getId()` + `getName()`. Superset → **no regression, REFUTED**. (`FolderResponse.parentFolderIds` etc. are read, but only off the standalone folder-API path — out of scope.)

## Related
[[materialize-code-review]]

## Related

- [[materialize-code-review]]
