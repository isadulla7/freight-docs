# Task 003: Backend Foundation

- **Status:** IN PROGRESS
- **Current checkpoint:** 3.1B — Spring Modulith boundaries + architecture tests
- **Implementation repository:** [`isadulla7/freight-backend`](https://github.com/isadulla7/freight-backend)
- **Architecture source:** [Architecture Context v1.0](../architecture/architecture-context-v1.md), [ADR-0001](../docs/architecture/decisions/0001-technology-baseline.md), [ADR-0002](../docs/architecture/decisions/0002-modular-monolith.md), and [module boundaries](../docs/architecture/module-boundaries.md)

## Objective

Establish an executable backend foundation whose Spring Modulith model enforces the accepted seven business-module boundaries and the intentionally tiny `shared` support module before business features are added.

## Checkpoints

| Checkpoint | Scope | Status | Review gate |
|---|---|---|---|
| 3.1A | Kotlin 2.4.10, Java 25, Spring Boot 4.1.0, Spring Modulith 2.1.0, Gradle wrapper, secure baseline configuration, package scaffold, and context smoke test | COMPLETE | [`freight-backend#1`](https://github.com/isadulla7/freight-backend/pull/1) |
| 3.1B | Explicit Spring Modulith module metadata, accepted dependency allow-list, and architecture verification tests | IN PROGRESS | Draft [`freight-backend#2`](https://github.com/isadulla7/freight-backend/pull/2) |

Later Backend Foundation checkpoints are out of scope until `NEXT-TASK.md` selects them.

## 3.1B scope

- declare `identity`, `accounts`, `fleet`, `freight`, `marketplace`, `shipment`, and `communication` as closed Spring Modulith application modules;
- declare `shared` as the common stable-primitives module and no other shared module;
- encode the dependency directions accepted in `docs/architecture/module-boundaries.md`;
- add an architecture test that discovers exactly the expected modules and executes Spring Modulith verification;
- update the backend README only where needed to describe the executable boundary check.

## Out of scope

- business use cases, domain aggregates, controllers, persistence, migrations, Redis, messaging brokers, authentication, or authorization implementation;
- named event interfaces before actual event contracts exist;
- database and local-infrastructure setup;
- changes to accepted dependency directions or other architecture decisions;
- later Backend Foundation checkpoints.

## Dependencies and prior gates

- Architecture v1.0 Lock: COMPLETE;
- API Contract v1: COMPLETE in [`freight-docs#4`](https://github.com/isadulla7/freight-docs/pull/4);
- Backend Foundation 3.1A: COMPLETE in [`freight-backend#1`](https://github.com/isadulla7/freight-backend/pull/1);
- start from the authoritative `freight-backend/main` SHA resolved from GitHub.

## Acceptance criteria

- the Spring Modulith model contains exactly `accounts`, `communication`, `fleet`, `freight`, `identity`, `marketplace`, `shared`, and `shipment`;
- all application modules are closed;
- every module declares a dependency allow-list consistent with the accepted dependency matrix, with `shared` available as the sole shared module;
- `ApplicationModules.of(FreightApplication::class.java).verify()` passes;
- cross-module cycles, access to another module's internal packages, and non-allow-listed module dependencies fail architecture verification;
- the existing application-context smoke test still passes;
- no business implementation or new infrastructure dependency is introduced.

## Required validation

- `./gradlew test` (or `gradlew.bat test` on Windows);
- `./gradlew build` (or `gradlew.bat build` on Windows);
- `git diff --check`;
- changed-file review and credential-pattern scan;
- verify the pull request targets the current `freight-backend/main` and contains only 3.1B scope.

## Expected Git output

- backend branch: `codex/backend-foundation-3-1b`;
- small logical commit for module metadata and architecture tests;
- pushed branch and draft pull request against `freight-backend/main`;
- status synchronization on a short-lived `freight-docs` branch and draft pull request.
