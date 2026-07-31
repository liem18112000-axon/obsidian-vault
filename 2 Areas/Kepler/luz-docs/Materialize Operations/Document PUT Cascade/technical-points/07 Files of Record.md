---
ai_hash: 9724aba32a59b67d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Files of Record
- document-put-cascade
- 06 Failure Paths
- 08 Glossary for Newbies
- PUT /documents/{document-id}
- materialized-field cascade
- DocumentResource.java
- updateDocumentMetadata(...)
- DocumentService.java
- MaterializeFacade.java
- shouldCascadeDocument(...)
- onDocumentChange(...)
- MaterializeCascadeService.java
- MaterializeRepository.java
- loadFoldersById(...)
- MaterializeCompute.java
- compute(...)
- MaterializeInput.java
- MaterializeState.java
- MaterializeConstants.java
- JsonObjectUtil.java
- getJsonArrayOrEmpty(...)
- mergeTechnicalFields(...)
- DocumentServiceValidation.java
- validateTechnicalFields(...)
- MaterializeCascadeServiceTest.java
- onDocumentChange tests
- shouldCascadeDocument tests
- diagrams/07-files-map.png
- File of record
- Test
- Utility
- Constant
- Facade
- Repository
- production code
- database
---

# Files of Record

[[document-put-cascade|Back to index]] | Previous: [[06 Failure Paths|failure paths]] | Next: [[08 Glossary for Newbies|glossary]]

These are the main files involved in `PUT /documents/{document-id}` materialized-field cascade.

## Source files

```text
src/main/java/ch/klara/luz/docs/rest/
└── DocumentResource.java
    └── updateDocumentMetadata(...)

src/main/java/ch/klara/luz/docs/service/
└── DocumentService.java
    └── updateDocumentMetadata(...)

src/main/java/ch/klara/luz/docs/materialize/
├── MaterializeFacade.java
│   ├── shouldCascadeDocument(...)
│   └── onDocumentChange(...)
├── MaterializeCascadeService.java
│   ├── onDocumentChange(...)
│   └── shouldCascadeDocument(...)
├── MaterializeRepository.java
│   └── loadFoldersById(...)
├── MaterializeCompute.java
│   └── compute(...)
├── MaterializeInput.java
├── MaterializeState.java
└── MaterializeConstants.java

src/main/java/ch/klara/luz/docs/utils/
└── JsonObjectUtil.java
    ├── getJsonArrayOrEmpty(...)
    └── mergeTechnicalFields(...)

src/main/java/ch/klara/luz/docs/validation/
└── DocumentServiceValidation.java
    └── validateTechnicalFields(...)
```

## Tests worth reading

```text
src/test/java/ch/klara/luz/docs/materialize/
└── MaterializeCascadeServiceTest.java
    ├── onDocumentChange tests
    └── shouldCascadeDocument tests
```

## Visual file map

![[diagrams/07-files-map.png]]

## Technical terms

| Term | Newbie explanation |
|---|---|
| File of record | The source file that owns the truth for a behavior. |
| Test | Automated code that checks whether production code behaves correctly. |
| Utility | Helper code reused by other parts of the app. |
| Constant | A named value reused by code to avoid typos and duplicated strings. |
| Facade | A simple front door over more detailed internal services. |
| Repository | Code responsible for talking to the database. |

%% ai-graph-start %%

**Related notes:**
- [[06 Files of Record]]
- [[document-put-cascade]]
- [[01 API Entry Point]]
- [[08 Glossary for Newbies]]
- [[folder-rename-cascade]]

**Relations:**
- Files of Record — *is_part_of* — document-put-cascade
- Files of Record — *follows* — 06 Failure Paths
- Files of Record — *precedes* — 08 Glossary for Newbies
- Files of Record — *describes* — PUT /documents/{document-id}
- PUT /documents/{document-id} — *involves* — materialized-field cascade
- Files of Record — *lists_source_file* — DocumentResource.java
- DocumentResource.java — *contains_method* — updateDocumentMetadata(...)
- Files of Record — *lists_source_file* — DocumentService.java
- DocumentService.java — *contains_method* — updateDocumentMetadata(...)
- Files of Record — *lists_source_file* — MaterializeFacade.java
- MaterializeFacade.java — *contains_method* — shouldCascadeDocument(...)
- MaterializeFacade.java — *contains_method* — onDocumentChange(...)
- Files of Record — *lists_source_file* — MaterializeCascadeService.java
- MaterializeCascadeService.java — *contains_method* — onDocumentChange(...)
- MaterializeCascadeService.java — *contains_method* — shouldCascadeDocument(...)
- Files of Record — *lists_source_file* — MaterializeRepository.java
- MaterializeRepository.java — *contains_method* — loadFoldersById(...)
- Files of Record — *lists_source_file* — MaterializeCompute.java
- MaterializeCompute.java — *contains_method* — compute(...)
- Files of Record — *lists_source_file* — MaterializeInput.java
- Files of Record — *lists_source_file* — MaterializeState.java
- Files of Record — *lists_source_file* — MaterializeConstants.java
- Files of Record — *lists_source_file* — JsonObjectUtil.java
- JsonObjectUtil.java — *contains_method* — getJsonArrayOrEmpty(...)
- JsonObjectUtil.java — *contains_method* — mergeTechnicalFields(...)
- Files of Record — *lists_source_file* — DocumentServiceValidation.java
- DocumentServiceValidation.java — *contains_method* — validateTechnicalFields(...)
- Files of Record — *lists_test_file* — MaterializeCascadeServiceTest.java
- MaterializeCascadeServiceTest.java — *tests* — onDocumentChange tests
- MaterializeCascadeServiceTest.java — *tests* — shouldCascadeDocument tests
- Files of Record — *has_visual_map* — diagrams/07-files-map.png
- File of record — *is_defined_as* — The source file that owns the truth for a behavior.
- Test — *is_defined_as* — Automated code that checks whether production code behaves correctly.
- Test — *checks* — production code
- Utility — *is_defined_as* — Helper code reused by other parts of the app.
- Constant — *is_defined_as* — A named value reused by code to avoid typos and duplicated strings.
- Facade — *is_defined_as* — A simple front door over more detailed internal services.
- Repository — *is_defined_as* — Code responsible for talking to the database.
- Repository — *talks_to* — database

%% ai-graph-end %%