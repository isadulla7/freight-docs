# Task 004: Local Infrastructure

| Field | Value |
|---|---|
| Status | **COMPLETE** |
| Phase | `[4] Local Infrastructure` |
| Prior gate | Backend Foundation (phase 3) reviewed and COMPLETE |
| Architecture baseline | [`architecture-context-v1.md`](../architecture/architecture-context-v1.md), version `v1.0` |

## Objective

Give `freight-backend` a reproducible local developer runtime for its accepted infrastructure dependencies, per [ADR-0004](../docs/architecture/decisions/0004-build-and-local-runtime.md): PostgreSQL/PostGIS, Redis, and S3-compatible object storage (MinIO), started with one documented command via Docker Compose.

This file is only the next-phase task specification. It does not select or execute a bounded subtask — that selection happens the same way each Backend Foundation checkpoint did: propose the smallest sensible subtask from the current repository state, confirm it, then implement it.

## Scope

- Local Docker Compose runtime covering the accepted local baseline: PostgreSQL/PostGIS, Redis, and MinIO.
- Container healthchecks so the stack's readiness is verifiable, not assumed.
- `.env.example` stays the template for any new environment variables the compose stack introduces; no real credentials committed.
- Documentation of the one command that starts the local stack.

## Out of scope

- Identity/Auth implementation
- Any business/domain module implementation
- Production or cloud deployment topology (ADR-0004 only requires reproducible local development; production evolution is a separate, later concern)
- Kubernetes or any production orchestrator
- Changes to accepted Architecture v1.0 decisions without a separately reviewed ADR

## Dependencies

- Backend Foundation (phase 3, `freight-docs` task 003) is COMPLETE.
- Each subtask starts from the then-current `freight-backend` GitHub `main` with no unresolved conflicting pull request.

## Acceptance criteria (per subtask)

- Each subtask ships as its own small, reviewed pull request in `freight-backend`.
- The full local stack (once complete) starts with one documented command and passes container healthchecks.
- `.env.example` documents only names and safe defaults; a secret scan finds nothing real.
- No subtask under this task introduces Identity/Auth or business-domain code.

## Required validation

- A reviewed pull request in `freight-backend` covering the subtask's exact scope.
- The subtask's own validation commands (existing test suite, build, CI pipeline) pass.
- Where applicable, a clean-environment run of the documented startup command.

## Expected Git output

- Implementation happens in `freight-backend`, not in `freight-docs`.
- This file is updated as each subtask starts and completes, the same way `tasks/003-backend-foundation.md` tracked Backend Foundation checkpoints.

## Subtasks

| Subtask | Scope | Status |
|---|---|---|
| 4.1 | Docker Compose foundation (skeleton, no services) | **COMPLETE** |
| 4.2 | PostgreSQL/PostGIS service | **COMPLETE** |
| 4.3 | Redis service | **COMPLETE** |
| 4.4 | MinIO service | **COMPLETE** |
| 4.5 | Full-stack startup/health validation | **COMPLETE** |
| 4.6 | Local Infrastructure closure gate | **COMPLETE** |

## Closure evidence (4.6)

Verified against `freight-backend` main `2a342fe3e8c1e3985e785b714a4001134e7164c4`:

- `docker compose config` valid; all three services (`postgres`, `redis`, `minio`) reached `healthy` together via `docker compose up -d`
- PostGIS `3.5`, Redis `PONG`, MinIO `:9000`/`:9001` both HTTP 200; `freight-local` network and both named volumes (`freight-postgres-data`, `freight-minio-data`) correctly attached
- `./gradlew clean test` — 37 tests / 0 skipped / 0 failures; `./gradlew build` — BUILD SUCCESSFUL
- `git diff --check` clean; secret scan clean
- `docker compose down` (no `-v`) — named volumes correctly survived
- No open `freight-backend` pull requests affecting phase 4

Phase 4 Local Infrastructure is COMPLETE. Phase 5 (Identity/Auth) is tracked in [tasks/005-identity-auth.md](005-identity-auth.md).
