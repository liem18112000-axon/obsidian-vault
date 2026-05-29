# API Entry Point

[[document-put-cascade|Back to index]] | Next: [[02 Service Validation|Service validation]]

## What this endpoint does

The endpoint is:

```http
PUT /{tenantId}/documents/{document-id}
```

It receives a JSON body containing the new full document metadata.

In `DocumentResource.java`, the method does only two things:

```java
documentService.updateDocumentMetadata(credentialToken, tenantId, documentId, documents);
return Response.ok().build();
```

So the REST resource is just the front door. The actual validation, materialized-field cascade, and database save happen inside `DocumentService.updateDocumentMetadata`.

## Visual explanation

![[diagrams/01-put-flow.png]]

## Beginner explanation

Imagine the REST method as a receptionist:

1. It receives the request.
2. It passes the request to the correct service.
3. If the service finishes successfully, it returns HTTP 200 OK.

It does not directly update MongoDB.

## Technical terms

| Term | Newbie explanation |
|---|---|
| REST resource | A Java class that exposes API endpoints. |
| Endpoint | One specific API action, like `PUT /documents/{id}`. |
| `@PUT` | Annotation saying this method handles HTTP PUT requests. |
| `@Path` | Annotation saying which URL path maps to this method. |
| JSON body | The data sent by the caller, shaped like JavaScript object text. |
| `Response.ok()` | Builds an HTTP 200 OK response. |

