# Task 003: Backend Foundation

- **Status:** IN PROGRESS
- **Current checkpoint:** 3.3A — PostgreSQL/PostGIS/Flyway/JPA/jOOQ foundation
- **Implementation repository:** [`isadulla7/freight-backend`](https://github.com/isadulla7/freight-backend)
- **Architecture source:** [Architecture Context v1.0](../architecture/architecture-context-v1.md), [ADR-0003](../docs/architecture/decisions/0003-persistence.md), [ADR-0013](../docs/architecture/decisions/0013-shared-database-ownership.md), [ADR-0014](../docs/architecture/decisions/0014-geospatial-representation.md), [database ownership](../docs/architecture/database-ownership.md), [database ERD](../docs/architecture/database-erd.md), and [module boundaries](../docs/architecture/module-boundaries.md)

## Objective

Establish the PostgreSQL/PostGIS persistence foundation with Flyway as schema authority, JPA for future transactional writes, and jOOQ for future complex reads, without adding business entities, repositories, or queries.

## Checkpoints

| Checkpoint | Scope | Status | Review gate |
|---|---|---|---|
| 3.1A | Kotlin 2.4.10, Java 25, Spring Boot 4.1.0, Spring Modulith 2.1.0, Gradle wrapper, secure baseline configuration, package scaffold, and context smoke test | COMPLETE | [`freight-backend#1`](https://github.com/isadulla7/freight-backend/pull/1) |
| 3.1B | Explicit Spring Modulith module metadata, accepted dependency allow-list, and architecture verification tests | COMPLETE | [`freight-backend#2`](https://github.com/isadulla7/freight-backend/pull/2) |
| 3.2 | Security and configuration foundation | COMPLETE | [`freight-backend#3`](https://github.com/isadulla7/freight-backend/pull/3) |
| 3.3A | PostgreSQL/PostGIS/Flyway/JPA/jOOQ foundation | IN PROGRESS | New draft `freight-backend` pull request |

Backend Foundation 3.3B and 3.4 are out of scope until 3.3A is reviewed and merged and `NEXT-TASK.md` selects the next checkpoint.

## 3.3A scope

- configure PostgreSQL with external credentials and typed, validated connection settings;
- add deterministic forward-only Flyway setup for PostGIS and the seven module-owned schemas only;
- preserve SRID 4326, `geography(Point,4326)`, and future GiST index conventions without creating location tables;
- add JPA/Hibernate and jOOQ foundations without entities, repositories, generated business code, or queries;
- keep Flyway authoritative and prevent Hibernate schema mutation;
- add placeholder-only database variables and focused configuration safety tests;
- preserve the 3.2 security baseline and Spring Modulith architecture tests.

## Out of scope

- business entities, tables, repositories, services, controllers, queries, or seed data;
- public-schema business tables, cross-module persistence access, or handwritten SQL repositories;
- Testcontainers, Docker Compose, Redis, JWT/OTP, Telegram, Kafka, Elasticsearch, or OpenAPI integration;
- changes to accepted dependency directions or the Java/Kotlin/Spring/Modulith/Gradle baseline;
- Backend Foundation 3.3B or 3.4.

## Dependencies and prior gates

- Architecture v1.0 Lock: COMPLETE;
- API Contract v1: COMPLETE in [`freight-docs#4`](https://github.com/isadulla7/freight-docs/pull/4);
- Backend Foundation 3.1A: COMPLETE in [`freight-backend#1`](https://github.com/isadulla7/freight-backend/pull/1);
- Backend Foundation 3.2: COMPLETE in [`freight-backend#3`](https://github.com/isadulla7/freight-backend/pull/3);
- start from authoritative `freight-backend/main` SHA `2fae5bb25cf6197dba485e8def8002105bc2a2a8`.

## Acceptance criteria

- PostgreSQL credentials remain external and connection settings bind safely;
- Flyway enables PostGIS and creates only `identity`, `accounts`, `fleet`, `freight`, `marketplace`, `shipment`, and `communication` schemas;
- Flyway is schema authority and Hibernate cannot create or update schemas;
- JPA and jOOQ load without business entities, repositories, generated code, or queries;
- focused configuration/Flyway/Hibernate safety tests pass alongside existing security and architecture tests;
- no secrets, business persistence, destructive migration, or cross-module coupling is introduced.

## Required validation

- `./gradlew test` (or `gradlew.bat test` on Windows);
- `./gradlew build` (or `gradlew.bat build` on Windows);
- `git diff --check`;
- changed-file review and credential-pattern scan;
- explicit scans for secrets, business entities/repositories, Hibernate `create`/`update`, destructive SQL, and cross-module persistence coupling;
- verify the pull request targets `2fae5bb25cf6197dba485e8def8002105bc2a2a8` and contains only 3.3A scope.

## Expected Git output

- backend branch: `codex/backend-foundation-3-3a`;
- small focused commits for persistence configuration/migrations and tests;
- pushed branch and draft pull request against `freight-backend/main`;
- separate docs-only status synchronization in draft [`freight-docs#5`](https://github.com/isadulla7/freight-docs/pull/5).
