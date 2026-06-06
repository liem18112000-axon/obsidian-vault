---
title: "Folder parent-change cascade is a no-op when the security-code union is unchanged"
created: 2026-06-04
type: lesson
status: seedling
source: "LUZ-154159 predicate refinement"
tags: [luz-docs, materialize, cascade, mongodb, performance]
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
