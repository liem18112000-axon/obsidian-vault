---
ai_hash: d2edaf3300848842
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities:
- Folder parent-change cascade
- security-code union
- parentFolderIds
- _folderSecurityClassCodes
- _effectiveSecurityClassCodes
- _isPublic
- folder.securityClassCodes
- folder.inheritedSecurityClassCodes
- preUnion
- postUnion
- shouldCascadeFolderParent
- Objects.equals
- Set.of()
- FolderService.cascadeFolderParentChangeIfNeeded
- currentMetadata.securityClassCodes
- currentMetadata.inheritedSecurityClassCode
- metadata.securityClassCodes
- getInheritedParentSecurityClass
- tenantId
- newParents
- empty union case
- _folderNames
- folder's own name
- oldParentIds
- newParentIds
- _folderNames is parent-chain-independent — depends only on each folder's own name
- reference_earchive_cascade_mode
- cascade pipeline
- guard
source: LUZ-154159 predicate refinement
status: seedling
tags:
- luz-docs
- materialize
- cascade
- mongodb
- performance
title: Folder parent-change cascade is a no-op when the security-code union is unchanged
type: lesson
---

# Folder parent-change cascade is a no-op when the security-code union is unchanged

When a folder's \`parentFolderIds\` changes, the parent-change cascade rewrites three sentinel fields on every descendant doc: \`_folderSecurityClassCodes\`, \`_effectiveSecurityClassCodes\`, \`_isPublic\`. All three are derived purely from the union \`folder.securityClassCodes ∪ folder.inheritedSecurityClassCodes\`. If that union is **unchanged** across the update, every descendant ends up with byte-identical sentinel fields — the cascade is wasted DB work.

## The predicate

\`\`\`java
boolean shouldCascadeFolderParent(Set<String> preUnion, Set<String> postUnion) {
    return !Objects.equals(
        preUnion == null ? Set.of() : preUnion,
        postUnion == null ? Set.of() : postUnion);
}
\`\`\`

Caller (\`FolderService.cascadeFolderParentChangeIfNeeded\`):
- \`preUnion\` = \`currentMetadata.securityClassCodes ∪ currentMetadata.inheritedSecurityClassCode\` (read from DB before the put).
- \`postUnion\` = \`metadata.securityClassCodes ∪ getInheritedParentSecurityClass(tenantId, newParents)\` (own from input, inherited recomputed from new parent chain).

## Strictly tighter than "empty union"

The first cut of this guard skipped only when both unions were empty. That misses real no-op cases like own=\`{A}\` + inherited=\`{B}\` before AND after — union \`{A,B}\` is non-empty but identical, so cascade still does nothing. The unchanged-union predicate subsumes the empty-union case (∅ == ∅) and catches every other equal-union case as well.

## Why parent-id equality isn't needed separately

If the user PUTs the same \`parentFolderIds\`, \`getInheritedParentSecurityClass(newParents)\` returns the same set as before, so \`postUnion == preUnion\` and the predicate naturally returns \`false\`. The old explicit \`Objects.equals(oldParentIds, newParentIds)\` short-circuit was redundant.

## What this guard does NOT cover

\`_folderNames\` is also rewritten by the cascade pipeline, but it's a per-folder name list that depends on each folder's own \`name\` — independent of the parent chain. See [[_folderNames is parent-chain-independent — depends only on each folder's own name]]. That's why the guard can be sound even though \`_folderNames\` is in the pipeline output.

## Related

- [[_folderNames is parent-chain-independent — depends only on each folder's own name]]
- [[reference_earchive_cascade_mode]]

%% ai-graph-start %%

**Related notes:**
- [[_folderNames is parent-chain-independent — depends only on each folder's own name]]
- [[Materialize folder parentFolderIds change cascade (LUZ-154159)]]
- [[03 Cascade Decision Gate]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]

**Relations:**
- Folder parent-change cascade — *is no-op when* — security-code union is unchanged
- parentFolderIds — *change triggers* — Folder parent-change cascade
- Folder parent-change cascade — *rewrites* — _folderSecurityClassCodes
- Folder parent-change cascade — *rewrites* — _effectiveSecurityClassCodes
- Folder parent-change cascade — *rewrites* — _isPublic
- _folderSecurityClassCodes — *derived from* — security-code union
- _effectiveSecurityClassCodes — *derived from* — security-code union
- _isPublic — *derived from* — security-code union
- security-code union — *is defined as* — folder.securityClassCodes ∪ folder.inheritedSecurityClassCodes
- shouldCascadeFolderParent — *is a predicate for* — Folder parent-change cascade
- shouldCascadeFolderParent — *compares* — preUnion
- shouldCascadeFolderParent — *compares* — postUnion
- shouldCascadeFolderParent — *uses* — Objects.equals
- shouldCascadeFolderParent — *uses* — Set.of()
- FolderService.cascadeFolderParentChangeIfNeeded — *calls* — shouldCascadeFolderParent
- preUnion — *is calculated from* — currentMetadata.securityClassCodes ∪ currentMetadata.inheritedSecurityClassCode
- preUnion — *is read from* — DB
- postUnion — *is calculated from* — metadata.securityClassCodes ∪ getInheritedParentSecurityClass(tenantId, newParents)
- postUnion — *is recomputed from* — new parent chain
- getInheritedParentSecurityClass — *takes* — tenantId
- getInheritedParentSecurityClass — *takes* — newParents
- shouldCascadeFolderParent — *subsumes* — empty union case
- _folderNames — *is rewritten by* — cascade pipeline
- _folderNames — *depends on* — folder's own name
- _folderNames — *is independent of* — parentFolderIds
- guard — *is sound despite* — _folderNames being in cascade pipeline
- Objects.equals(oldParentIds, newParentIds) short-circuit — *was* — redundant
- Folder parent-change cascade — *related to* — _folderNames is parent-chain-independent — depends only on each folder's own name
- Folder parent-change cascade — *related to* — reference_earchive_cascade_mode

%% ai-graph-end %%