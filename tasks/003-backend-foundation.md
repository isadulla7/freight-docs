# Task 003: Backend Foundation

| Field | Value |
|---|---|
| Status | **IN PROGRESS** |
| Phase | `[3] Backend Foundation` |
| Prior gate | API Contract v1 reviewed and merged |
| Architecture baseline | [`architecture-context-v1.md`](../architecture/architecture-context-v1.md), version `v1.0` |

## Objective

Establish the `freight-backend` scaffold, module boundaries, security posture, persistence foundation, and supporting infrastructure through small, independently reviewed checkpoints, before any Identity/Auth or business-domain implementation begins.

This file tracks checkpoint status only. It does not restate accepted architecture; see the canonical documents linked from [`AGENTS.md`](../AGENTS.md).

## Checkpoints

| Checkpoint | Description | Status |
|---|---|---|
| 3.1A | Minimal backend scaffold | **COMPLETE** |
| 3.1B | Spring Modulith boundaries and enforcement tests | **COMPLETE** |
| 3.2 | Security/config foundation (deny-by-default, stateless, CORS) | **COMPLETE** |
| 3.3A | PostgreSQL/PostGIS/Flyway/JPA/jOOQ foundation | **COMPLETE** |
| 3.3B | PostgreSQL/PostGIS integration validation | **COMPLETE** |
| 3.4A | CI foundation (GitHub Actions build/test gate) | **COMPLETE** |
| 3.4B | Redis foundation (OTP/rate-limit/hot cache/session metadata store, per [ADR-0003](../docs/architecture/decisions/0003-persistence.md) and [ADR-0004](../docs/architecture/decisions/0004-build-and-local-runtime.md)) | **CURRENT** |

## Out of scope

- Identity/Auth implementation
- Any business/domain module implementation (accounts, fleet, freight, marketplace, shipment, communication)
- OpenAPI/contract integration into `freight-backend` (contract artifacts are owned by `freight-contracts` per [ADR-0007](../docs/architecture/decisions/0007-openapi-contract.md))
- Changes to accepted Architecture v1.0 decisions without a separately reviewed ADR

## Dependencies

- API Contract v1 is merged (`freight-docs` PR #4).
- Each checkpoint starts from the then-current `freight-backend` GitHub `main` with no unresolved conflicting pull request.

## Acceptance criteria (per checkpoint)

- Each checkpoint ships as its own small, reviewed pull request in `freight-backend`.
- Each checkpoint preserves Spring Modulith module boundaries, deny-by-default security, and Flyway as the sole schema authority.
- Each checkpoint's validation is recorded in its `freight-backend` pull request and reflected in this file and `HANDOFF.md`.
- No checkpoint under this task introduces Identity/Auth or business-domain code.

## Required validation

- A reviewed pull request in `freight-backend` covering the checkpoint's exact scope.
- The checkpoint's own validation commands (tests, build, and from 3.4A onward, the CI pipeline) pass.

## Expected Git output

- Checkpoint implementation happens in `freight-backend`, not in `freight-docs`.
- This file is updated as each checkpoint moves from `CURRENT` to `COMPLETE` and the next checkpoint is selected.
