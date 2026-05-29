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

