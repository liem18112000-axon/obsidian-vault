---
title: "Encode a benign-error decision in a dedicated exception type, not a swallowed catch on a magic status code"
created: 2026-06-08
type: lesson
status: seedling
source: "luz_docs materialize, 2026-06-08"
tags: [java, exceptions, design, fault-tolerance, luz-docs, materialize]
---

# Encode a benign-error decision in a dedicated exception type, not a swallowed catch on a magic status code

When a downstream call returns a status that is technically an "error" but is actually a benign no-op for your use case, do not encode that decision as a magic-status check plus an empty/opaque catch. Give the benign case its **own exception type** and catch *that* type explicitly. The type name carries the intent; the empty catch does not.

## Why
- An empty `catch (GenericException e) {}` reads as "swallowed bug", not "deliberate no-op" — reviewers cannot tell intent from mechanism.
- A magic status check (`e.getHttpStatusCode() == SC_MULTI_STATUS`) buries the meaning in a number.
- A named type also interacts correctly with fault-tolerance interceptors: a distinct class can be excluded from `retryOn`/included in `abortOn`/`skipOn` so a deterministic benign condition is neither retried forever nor sent through a rollback fallback.

## Pattern
1. Throw site translates the raw condition into the named exception:
   `if (e.getHttpStatusCode() == SC_MULTI_STATUS) throw new XMultiStatusException(msg, e);`
2. Consumer catches the named type and no-ops with a comment + log; everything else stays an error and propagates.

## Concrete instance — luz_docs materialize cascade
A jsonstore pipeline `updateMany` that recomputes identical values returns HTTP 207 (matchedCount > modifiedCount) — benign. Old code: inner method `throw e` (raw `DocumentException`) + `cascadeWithRetry` had `catch (DocumentException e) {}` (empty) to swallow it. Replaced with `MaterializeMultiStatusException` thrown at the 207 site and caught explicitly in `cascadeWithRetry` as a logged no-op. Real failures still surface as `MaterializeCascadeException` (the `@Retry retryOn` type) → retry → `onCascadeFailed` rollback. Behavior identical; intent now legible and type-checked.

## Related
[[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
[[materialize-code-review]]

## Related

- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[materialize-code-review]]
