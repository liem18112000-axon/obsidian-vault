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

