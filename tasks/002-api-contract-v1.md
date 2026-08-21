# Task 002: API Contract v1

| Field | Value |
|---|---|
| Status | **NEXT** |
| Phase | `[2] API Contract v1` |
| Prior gate | Agent Handoff System reviewed and merged |
| Architecture baseline | [`architecture-context-v1.md`](../architecture/architecture-context-v1.md), version `v1.0` |

## Objective

Define the reviewed, versioned API Contract v1 before application implementation begins. OpenAPI is the source of truth for the HTTP contract, consistent with [ADR-0007](../docs/architecture/decisions/0007-openapi-contract.md) and the accepted architecture.

This file is only the next-phase task specification. Do not execute the task as part of the Agent Handoff System pull request.

## Scope

Define the `/api/v1` contract at a high level and then capture it in the phase's reviewed OpenAPI artifacts. The contract must cover:

- common HTTP API conventions and media types;
- authentication and session-facing contract;
- users and accounts;
- companies and company membership where exposed;
- vehicles and availability where exposed;
- loads;
- offers;
- shipments;
- initial communication endpoints required by Architecture v1.0;
- cursor-based pagination for collection endpoints;
- one stable, machine-readable error model;
- UUID representation for public and domain identifiers;
- UTC timestamp representation;
- money as a lossless integer/safe-integer amount plus ISO-style currency, never floating point;
- geolocation as longitude/latitude values consistent with the accepted geospatial ADR;
- authorization expectations, permission requirements, tenant/resource scope, and non-disclosure behavior;
- compatibility expectations for Dart and TypeScript client generation in a later phase.

Use the existing [domain model](../docs/architecture/domain-model.md), [state machines](../docs/architecture/state-machines.md), [authorization model](../docs/architecture/authorization-model.md), and [event catalog](../docs/architecture/event-catalog.md) as constraints. Link to those sources instead of restating their architecture decisions.

## Out of scope

- Backend implementation
- Flutter implementation
- Next.js implementation
- Payment APIs
- Live tracking APIs
- Advanced dispute flows
- Generated Dart or TypeScript client integration
- Changes to accepted Architecture v1.0 decisions without a separately reviewed ADR

## Dependencies

- Architecture v1.0 Lock is merged into `main`.
- Agent Handoff System is reviewed and merged.
- The task starts from the then-current GitHub `main` branch with no unresolved conflicting pull request.
- Relevant ADRs are reviewed, especially [OpenAPI contract ownership](../docs/architecture/decisions/0007-openapi-contract.md), [repository strategy](../docs/architecture/decisions/0009-repository-strategy.md), [data conventions](../docs/architecture/decisions/0011-domain-data-conventions.md), [geospatial representation](../docs/architecture/decisions/0014-geospatial-representation.md), and [authorization and audit](../docs/architecture/decisions/0015-authorization-and-audit.md).

## Acceptance criteria

- Every in-scope capability has a documented endpoint set, request/response schema, authorization expectation, and applicable error outcomes.
- The contract uses `/api/v1`, UUIDs, UTC timestamps, lossless integer money with currency, and the accepted longitude/latitude ordering consistently.
- Collection endpoints use one cursor-pagination convention.
- Errors use one stable envelope and stable error codes.
- Resource and company scoping is explicit enough to prevent client-side authorization assumptions.
- OpenAPI artifacts are the single source of truth for the HTTP contract; prose links to them instead of duplicating schemas.
- Contract shapes align with Architecture v1.0 aggregate boundaries and state transitions without adding application behavior.
- Dart and TypeScript client generation feasibility is validated, but client applications are not implemented.
- All phase documents and indexes link to the canonical contract location.
- The pull request contains no backend, Flutter, Next.js, infrastructure, payment, live-tracking, or advanced-dispute implementation.

## Required validation

- Validate and lint the OpenAPI document with the selected repository tooling.
- Resolve and validate all `$ref` entries and relative Markdown links.
- Check endpoint, schema, permission, state, UUID, time, money, and geolocation naming against Architecture v1.0 documents.
- Check example payloads against their schemas.
- Verify the diff contains no secrets, credentials, generated application code, or out-of-scope implementation.
- Record validation commands and results in the pull request and `HANDOFF.md`.

## Expected Git output

- A short-lived `codex/` feature branch created from the then-current `main`.
- One or a few small logical documentation/contract commits.
- The branch pushed to GitHub.
- A draft pull request targeting `main`, with validation results and any genuine blockers recorded.
- No automatic merge; API Contract v1 remains a review gate.
