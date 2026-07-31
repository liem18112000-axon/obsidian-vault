---
tags: [luz-docs, materialize, gotcha, json]
created: 2026-06-02
---

# `userSecurityClassCodes` param must be JSON array text, not comma-separated

`MaterializeResponseBuilder.addFolderObjects(JsonArray, String userSecurityClassCodes)` takes the user codes as a JSON-array-encoded **string**, e.g. `"[\"SC_A\",\"SC_B\"]"` — not a comma-separated list like `"SC_A,SC_B"` and not a bare value like `"SC_A"`.

Internally it goes through `createArrayByString(codes)` which calls `Json.createReader(...).readArray()`. A bare or comma-separated string parses as malformed JSON and throws:

```
JsonParsing Unexpected char 83 at (line no=1, column no=1, offset=0)
```

(Char 83 = `'S'`, the first letter of `SC_…`.)

## Sentinels

- `null` / `""` → `Set.of()` (no user codes; filter rejects every restricted folder).
- `"[]"` (`Constants.EMPTY_ARRAY_SECURITYCLASS`) → also `Set.of()`.
- `"[\"X\"]"` → `{ "X" }`.

## Lesson for tests

The pre-existing tests on `kepler/sprint-157/add-new-folder-security-code-materialized-fields` passed comma-separated / bare strings and threw `JsonParsing` in CI — locally they appeared to pass because the surrounding mvn invocation suppressed surefire output. Always check the surefire report, don't trust `mvn -q test` exit codes alone.

Related: [[Parameterize JUnit5 tests across overload variants with Named Function MethodSource]]