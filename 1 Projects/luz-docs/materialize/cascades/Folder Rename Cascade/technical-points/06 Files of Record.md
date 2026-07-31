---
ai_hash: 6eee768a9c1ceea5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Files of Record
- folder rename cascade
- index
- 05 Retry Flow
- Retry flow
- 07 Operational Notes
- operational notes
- src/main/java/ch/klara/luz/docs/materialize/
- MaterializeCascadeMarker.java
- row schema
- column constants
- factory
- MaterializeCascadeStatus.java
- enum
- START
- PARTIAL
- MaterializeConstants.java
- FOLDER_IDS_REF
- FOLDER_NAMES_REF
- CASCADE_RETRY_BATCH
- MaterializeFacade.java
- onFolderNameChange
- token
- tenantId
- folderId
- newName
- MaterializeFolderRenameEvent.java
- record
- markerId
- MaterializeFolderRenameService.java
- sync fire
- '@ObservesAsync observer'
- onCascadeRetry
- MaterializeQueryBuilder.java
- buildFolderRenameFilter
- buildFolderRenamePipeline
- MaterializeRepository.java
- cascadeFolderNameInDocuments
- marker CRUD
- loadFolderName
- MaterializeRequestFilter.java
- MaterializeEvent
- MaterializeRetryEvent
- src/test/java/ch/klara/luz/docs/materialize/
- MaterializeComputeTest.java
- computeFolderNames
- MaterializeFolderRenameServiceTest.java
- MaterializeQueryBuilderTest.java
- folder-rename filter
- pipeline
- MaterializeRepositoryTest.java
- src/test/java/ch/klara/luz/docs/utils/
- JsonObjectUtilTest.java
- flattenUniqueStrings
- pickInOrder
- Source file
- Test file
- CRUD
- Constant
- Schema
- application code
- automated checks
- Java class
- data
- database operations
- value
- expected shape of stored data
- marker row
---

# Files of Record

[[folder-rename-cascade|Back to index]] | Previous: [[05 Retry Flow|Retry flow]] | Next: [[07 Operational Notes|operational notes]]

These are the main source files involved in folder rename cascade.

```text
src/main/java/ch/klara/luz/docs/materialize/
├── MaterializeCascadeMarker.java       - row schema, column constants, factory
├── MaterializeCascadeStatus.java       - enum { START, PARTIAL }
├── MaterializeConstants.java           - FOLDER_IDS_REF, FOLDER_NAMES_REF, CASCADE_RETRY_BATCH
├── MaterializeFacade.java              - onFolderNameChange(token, tenantId, folderId, newName)
├── MaterializeFolderRenameEvent.java   - record (tenantId, folderId, newName, markerId, token)
├── MaterializeFolderRenameService.java - sync fire, @ObservesAsync observer, onCascadeRetry
├── MaterializeQueryBuilder.java        - buildFolderRenameFilter / buildFolderRenamePipeline
├── MaterializeRepository.java          - cascadeFolderNameInDocuments, marker CRUD, loadFolderName
├── MaterializeRequestFilter.java       - fires both MaterializeEvent and MaterializeRetryEvent
└── MaterializeRetryEvent.java          - record (tenantId, token)
```

## Tests

```text
src/test/java/ch/klara/luz/docs/materialize/
├── MaterializeComputeTest.java              - + 4 cases for computeFolderNames
├── MaterializeFolderRenameServiceTest.java  - initial / retry / partial / exception / drain
├── MaterializeQueryBuilderTest.java         - + 4 cases for folder-rename filter + pipeline
├── MaterializeRepositoryTest.java           - + cascade 207 / timeout / happy + marker CRUD
src/test/java/ch/klara/luz/docs/utils/
└── JsonObjectUtilTest.java                  - + flattenUniqueStrings + pickInOrder
```

## Beginner terms

| Term | Beginner explanation |
|---|---|
| Source file | A file containing application code. |
| Test file | A file containing automated checks that prove the code behaves as expected. |
| Record | A compact Java class meant mainly to carry data. |
| CRUD | Create, Read, Update, Delete. Basic database operations. |
| Constant | A value named in code so it can be reused safely. |
| Schema | The expected shape of stored data, such as which fields a marker row has. |

%% ai-graph-start %%

**Related notes:**
- [[07 Files of Record]]
- [[07 Operational Notes]]
- [[folder-rename-cascade]]
- [[02 Trigger Flow]]
- [[05 Retry Flow]]

**Relations:**
- Files of Record — *describes* — folder rename cascade
- Files of Record — *links to index* — index
- Files of Record — *links to previous* — 05 Retry Flow
- 05 Retry Flow — *is also known as* — Retry flow
- Files of Record — *links to next* — 07 Operational Notes
- 07 Operational Notes — *is also known as* — operational notes
- MaterializeCascadeMarker.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeCascadeMarker.java — *is a type of* — Source file
- MaterializeCascadeMarker.java — *defines* — row schema
- MaterializeCascadeMarker.java — *defines* — column constants
- MaterializeCascadeMarker.java — *acts as a* — factory
- MaterializeCascadeStatus.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeCascadeStatus.java — *is a type of* — Source file
- MaterializeCascadeStatus.java — *is an* — enum
- MaterializeCascadeStatus.java — *includes value* — START
- MaterializeCascadeStatus.java — *includes value* — PARTIAL
- MaterializeConstants.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeConstants.java — *is a type of* — Source file
- MaterializeConstants.java — *defines* — FOLDER_IDS_REF
- MaterializeConstants.java — *defines* — FOLDER_NAMES_REF
- MaterializeConstants.java — *defines* — CASCADE_RETRY_BATCH
- MaterializeFacade.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeFacade.java — *is a type of* — Source file
- MaterializeFacade.java — *contains method* — onFolderNameChange
- onFolderNameChange — *takes parameter* — token
- onFolderNameChange — *takes parameter* — tenantId
- onFolderNameChange — *takes parameter* — folderId
- onFolderNameChange — *takes parameter* — newName
- MaterializeFolderRenameEvent.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeFolderRenameEvent.java — *is a type of* — Source file
- MaterializeFolderRenameEvent.java — *is a* — record
- MaterializeFolderRenameEvent.java — *contains field* — tenantId
- MaterializeFolderRenameEvent.java — *contains field* — folderId
- MaterializeFolderRenameEvent.java — *contains field* — newName
- MaterializeFolderRenameEvent.java — *contains field* — markerId
- MaterializeFolderRenameEvent.java — *contains field* — token
- MaterializeFolderRenameService.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeFolderRenameService.java — *is a type of* — Source file
- MaterializeFolderRenameService.java — *handles* — sync fire
- MaterializeFolderRenameService.java — *is an* — @ObservesAsync observer
- MaterializeFolderRenameService.java — *contains method* — onCascadeRetry
- MaterializeQueryBuilder.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeQueryBuilder.java — *is a type of* — Source file
- MaterializeQueryBuilder.java — *contains method* — buildFolderRenameFilter
- MaterializeQueryBuilder.java — *contains method* — buildFolderRenamePipeline
- MaterializeRepository.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeRepository.java — *is a type of* — Source file
- MaterializeRepository.java — *contains method* — cascadeFolderNameInDocuments
- MaterializeRepository.java — *performs* — marker CRUD
- MaterializeRepository.java — *contains method* — loadFolderName
- MaterializeRequestFilter.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeRequestFilter.java — *is a type of* — Source file
- MaterializeRequestFilter.java — *fires* — MaterializeEvent
- MaterializeRequestFilter.java — *fires* — MaterializeRetryEvent
- MaterializeRetryEvent.java — *is located in* — src/main/java/ch/klara/luz/docs/materialize/
- MaterializeRetryEvent.java — *is a type of* — Source file
- MaterializeRetryEvent.java — *is a* — record
- MaterializeRetryEvent.java — *contains field* — tenantId
- MaterializeRetryEvent.java — *contains field* — token
- MaterializeComputeTest.java — *is located in* — src/test/java/ch/klara/luz/docs/materialize/
- MaterializeComputeTest.java — *is a type of* — Test file
- MaterializeComputeTest.java — *tests method* — computeFolderNames
- MaterializeFolderRenameServiceTest.java — *is located in* — src/test/java/ch/klara/luz/docs/materialize/
- MaterializeFolderRenameServiceTest.java — *is a type of* — Test file
- MaterializeQueryBuilderTest.java — *is located in* — src/test/java/ch/klara/luz/docs/materialize/
- MaterializeQueryBuilderTest.java — *is a type of* — Test file
- MaterializeQueryBuilderTest.java — *tests* — folder-rename filter
- MaterializeQueryBuilderTest.java — *tests* — pipeline
- MaterializeRepositoryTest.java — *is located in* — src/test/java/ch/klara/luz/docs/materialize/
- MaterializeRepositoryTest.java — *is a type of* — Test file
- MaterializeRepositoryTest.java — *tests* — marker CRUD
- JsonObjectUtilTest.java — *is located in* — src/test/java/ch/klara/luz/docs/utils/
- JsonObjectUtilTest.java — *is a type of* — Test file
- JsonObjectUtilTest.java — *tests method* — flattenUniqueStrings
- JsonObjectUtilTest.java — *tests method* — pickInOrder
- Source file — *contains* — application code
- Test file — *contains* — automated checks
- record — *is a* — Java class
- record — *carries* — data
- CRUD — *represents* — database operations
- Constant — *is a* — value
- Schema — *describes* — expected shape of stored data
- Schema — *applies to* — marker row

%% ai-graph-end %%