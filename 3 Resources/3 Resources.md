---
title: "Resources"
type: moc
---

# Resources

> Atomic notes and reusable technical lessons, organized by strongly related concepts.

## AI Tools

### Claude Code

- [[3 Resources/AI Tools/Claude Code/Hooks/Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Claude Code hooks cannot run slash commands or clear-compact; they only inject additionalContext]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Claude Code hooks see no token usage in their payload; read the transcript usage entries instead]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Hook files referenced by external skills via hardcoded paths cannot be relocated]]
- [[3 Resources/AI Tools/Claude Code/Hooks/PowerShell pipe appends a newline to native-command stdin, shifting any hash]]
- [[3 Resources/AI Tools/Claude Code/Hooks/PSScriptRoot-relative state breaks when a hook moves to a subfolder]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Relocating a hardcoded-path hook integration self-locate or patch every reference site]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Test Windows stdin-JSON hooks with python json.dumps, not bash backslash strings]]

## DevOps

### Containers and Registries

- [[3 Resources/DevOps/Containers and Registries/Export build artifacts from a multi-stage Docker build via a scratch stage + buildx --output]]
- [[3 Resources/DevOps/Containers and Registries/GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern]]
- [[3 Resources/DevOps/Containers and Registries/Trivy scan-before-push needs a single-arch load build first]]

### Cloud Run and GCP

- [[3 Resources/DevOps/Cloud Run and GCP/Cloud Build repo connection blocked drive build+deploy from GitHub Actions instead]]
- [[3 Resources/DevOps/Cloud Run and GCP/IAM roles a CI service account needs to build and deploy to Cloud Run]]

## Version Control

### Git

- [[3 Resources/Version Control/Git/FETCH_HEAD is volatile when an IDE auto-fetches]]
- [[3 Resources/Version Control/Git/Split intermixed single-file changes into two commits via backup and intermediate edit]]

## Programming


### Data Formats — JSON Patch

- [[3 Resources/Programming/Data Formats/JSON Patch/RFC-6902 replace at array index expects a scalar element not an array]]
### Java — Core Language

- [[3 Resources/Programming/Java/Core Language/Adding a field to a Java record breaks all factory and constructor calls in tests]]
- [[3 Resources/Programming/Java/Core Language/Visibility downgrade breaks external callers]]

### Java — CDI and MicroProfile

- [[3 Resources/Programming/Java/CDI and MicroProfile/CDI self-invocation bypasses interceptor proxy]]
- [[3 Resources/Programming/Java/CDI and MicroProfile/CDI decorators and interceptors never fire on MicroProfile REST client proxies]]
- [[3 Resources/Programming/Java/CDI and MicroProfile/Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource]]
- [[3 Resources/Programming/Java/CDI and MicroProfile/MicroProfile Fault Tolerance annotations are inert on self-invoked methods]]
- [[3 Resources/Programming/Java/CDI and MicroProfile/Preserve compensation state when rollback itself fails]]
- [[3 Resources/Programming/Java/CDI and MicroProfile/Snapshot for rollback must live outside retry boundary]]

### Java — Testing

- [[3 Resources/Programming/Java/Testing/A merged-in test breaks when the target branch's service gained a new injected dependency]]
- [[3 Resources/Programming/Java/Testing/Interaction-style mocks hide ordering bugs that a stateful in-memory fake exposes]]
- [[3 Resources/Programming/Java/Testing/Mock at the facade boundary after consolidating logic behind a facade method]]
- [[3 Resources/Programming/Java/Testing/Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures]]
- [[3 Resources/Programming/Java/Testing/New collaborator call NPEs old @InjectMocks tests]]
- [[3 Resources/Programming/Java/Testing/Parameterize JUnit5 tests across overload variants with Named Function MethodSource]]

### Java — JSON-P

- [[3 Resources/Programming/Java/JSON-P/javax.json JsonObject is immutable so aliasing replaces defensive deep copies]]

### Python — Imports and Paths

- [[3 Resources/Programming/Python/Imports and Paths/Flat-import Python modules can be relocated together without rewriting imports]]
- [[3 Resources/Programming/Python/Imports and Paths/Path(__file__).parent breaks when a module is moved to a deeper directory]]

## Product Notes

### luz-docs — Materialize

- [[3 Resources/Product/luz-docs/Materialize/Empty per-folder codes means public, not no-access]]
- [[3 Resources/Product/luz-docs/Materialize/Marker plus retry-on-next-request pattern for async cascade recovery]]
- [[3 Resources/Product/luz-docs/Materialize/Parallel arrays in materialize sentinel preserve folderId order]]
- [[3 Resources/Product/luz-docs/Materialize/userSecurityClassCodes param must be JSON array text not comma-separated]]

### luz-docs — Utils

- [[3 Resources/Product/luz-docs/Utils/JsonObjectUtil.convertJsonArrayToListString unwraps JsonString already]]

## Software Engineering

### API and Design Patterns

- [[3 Resources/Software Engineering/API and Design Patterns/Pick the variant matching the data you already hold, not the triggering operation]]

### Schema Evolution

- [[3 Resources/Software Engineering/Schema Evolution/Absent discriminator field as legacy default in evolving document schemas]]

## Operating Systems

### Windows

- [[3 Resources/Operating Systems/Windows/winget PATH update only applies to new shells, not the current session]]

