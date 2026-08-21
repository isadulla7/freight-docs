# Task 003: Backend Foundation

- **Status:** IN PROGRESS
- **Current checkpoint:** 3.2 — Security and configuration foundation
- **Implementation repository:** [`isadulla7/freight-backend`](https://github.com/isadulla7/freight-backend)
- **Architecture source:** [Architecture Context v1.0](../architecture/architecture-context-v1.md), [ADR-0001](../docs/architecture/decisions/0001-technology-baseline.md), [ADR-0002](../docs/architecture/decisions/0002-modular-monolith.md), [ADR-0010](../docs/architecture/decisions/0010-privacy-and-security-baseline.md), [ADR-0015](../docs/architecture/decisions/0015-authorization-and-audit.md), [security baseline](../docs/security/security-baseline.md), and [module boundaries](../docs/architecture/module-boundaries.md)

## Objective

Establish a production-safe backend foundation with executable module boundaries, deny-by-default HTTP security, safe CORS defaults, typed validated configuration, and minimal actuator/error exposure before authentication or business features are added.

## Checkpoints

| Checkpoint | Scope | Status | Review gate |
|---|---|---|---|
| 3.1A | Kotlin 2.4.10, Java 25, Spring Boot 4.1.0, Spring Modulith 2.1.0, Gradle wrapper, secure baseline configuration, package scaffold, and context smoke test | COMPLETE | [`freight-backend#1`](https://github.com/isadulla7/freight-backend/pull/1) |
| 3.1B | Explicit Spring Modulith module metadata, accepted dependency allow-list, and architecture verification tests | COMPLETE | [`freight-backend#2`](https://github.com/isadulla7/freight-backend/pull/2) |
| 3.2 | Security and configuration foundation | IN PROGRESS | New draft `freight-backend` pull request |

Backend Foundation 3.3 is out of scope until 3.2 is reviewed and merged and `NEXT-TASK.md` selects it.

## 3.2 scope

- add an explicit `SecurityFilterChain` that denies anonymous access by default and permits only explicitly intended infrastructure endpoints;
- keep actuator web exposure limited to `health` and `info`, with health details hidden;
- add typed CORS configuration with an allow-nothing default, no wildcard origin, and no wildcard/credentials combination;
- add typed `@ConfigurationProperties` and validation for security-sensitive settings, including fail-fast rejection of invalid production configuration where appropriate;
- keep stack traces and internal exception messages hidden without inventing business API errors;
- keep `.env.example` placeholder-only and all secrets external to source;
- add focused tests for security loading, protected request denial, explicit infrastructure access, CORS safety, actuator exposure, and configuration validation;
- preserve all Spring Modulith declarations and architecture tests.

## Out of scope

- JWT, OTP, login/register endpoints, users, sessions, or authorization business rules;
- business controllers, domain use cases, persistence, database, migrations, Redis, or Redis-backed rate limiting;
- OpenAPI integration, Telegram, Docker, or infrastructure;
- custom roles/capabilities or business error types;
- unnecessary dependencies or changes to the Java/Kotlin/Spring/Gradle baseline;
- changes to accepted dependency directions or other architecture decisions;
- Backend Foundation 3.3.

## Dependencies and prior gates

- Architecture v1.0 Lock: COMPLETE;
- API Contract v1: COMPLETE in [`freight-docs#4`](https://github.com/isadulla7/freight-docs/pull/4);
- Backend Foundation 3.1A: COMPLETE in [`freight-backend#1`](https://github.com/isadulla7/freight-backend/pull/1);
- Backend Foundation 3.1B: COMPLETE in [`freight-backend#2`](https://github.com/isadulla7/freight-backend/pull/2);
- start from authoritative `freight-backend/main` SHA `326b5a6621b6df44a9ad03c057fc2614a82aedaf`.

## Acceptance criteria

- an explicit security filter chain loads and denies application endpoints to anonymous requests by default;
- only explicitly intended infrastructure endpoints are anonymous, with no broad `permitAll` rule;
- CORS defaults to no allowed origins and rejects wildcard origins or unsafe wildcard/credentials configuration;
- allowed origins come from validated typed configuration rather than hardcoded production domains;
- invalid security-sensitive production configuration fails application startup;
- stack traces, internal messages, actuator health details, and sensitive actuator endpoints are not exposed;
- focused tests cover the required positive and negative cases;
- all existing application-context and Spring Modulith architecture tests still pass;
- no authentication/business implementation or unnecessary dependency is introduced.

## Required validation

- `./gradlew test` (or `gradlew.bat test` on Windows);
- `./gradlew build` (or `gradlew.bat build` on Windows);
- `git diff --check`;
- changed-file review and credential-pattern scan;
- explicit scans for wildcard CORS, broad `permitAll`, sensitive actuator endpoints, and accidental business logic;
- verify the pull request targets `326b5a6621b6df44a9ad03c057fc2614a82aedaf` and contains only 3.2 scope.

## Expected Git output

- backend branch: `codex/backend-foundation-3-2`;
- small focused commits for configuration/security foundation and its tests;
- pushed branch and draft pull request against `freight-backend/main`;
- separate docs-only status synchronization in draft [`freight-docs#5`](https://github.com/isadulla7/freight-docs/pull/5).
