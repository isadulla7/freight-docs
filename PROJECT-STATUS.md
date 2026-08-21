# Project status

This file is a navigation summary. Accepted architecture remains in the [canonical context](architecture/architecture-context-v1.md) and [ADRs](docs/architecture/decisions/README.md).

## Current state

| Field | Value |
|---|---|
| Current architecture version | `v1.0` |
| Authoritative `main` | `b6f759f8a14dbf07ce6350e814ddd14cbe740fc1` |
| Agent Handoff System merge SHA | `81ac2cb7023dd33329d8ab3e4b8d1316d40dd3c5` |
| Latest completed architecture gate | Architecture v1.0 Lock |
| Current task | `[3.3B] Backend Foundation` — PostgreSQL/PostGIS integration validation with Testcontainers |
| Task specification | [`tasks/003-backend-foundation.md`](tasks/003-backend-foundation.md) |
| Implementation repository | [`isadulla7/freight-backend`](https://github.com/isadulla7/freight-backend) |

## Roadmap

| Phase | Checkpoint | Status |
|---:|---|---|
| 0 | Canonical Context | **COMPLETE** |
| 1 | Architecture v1.0 Lock | **COMPLETE** |
| 1.5 | Agent Handoff System | **COMPLETE** |
| 2 | API Contract v1 | **COMPLETE** |
| 3 | Backend Foundation | **IN PROGRESS** |
| 4 | Local Infrastructure | **PENDING** |
| 5 | Identity/Auth | **PENDING** |
| 6 | Accounts | **PENDING** |
| 7 | Fleet | **PENDING** |
| 8 | Freight | **PENDING** |
| 9 | Marketplace | **PENDING** |
| 10 | Shipment | **PENDING** |
| 11 | Communication | **PENDING** |
| 12 | Mobile/Web | **PENDING** |

## Backend Foundation checkpoints

| Checkpoint | Scope | Status |
|---|---|---|
| 3.1A | Kotlin/Spring Boot project scaffold | **COMPLETE** |
| 3.1B | Spring Modulith boundaries + architecture tests | **COMPLETE** |
| 3.2 | Security and configuration foundation | **COMPLETE** |
| 3.3A | PostgreSQL/PostGIS/Flyway/JPA/jOOQ foundation | **COMPLETE** |
| 3.3B | PostgreSQL/PostGIS integration validation with Testcontainers | **IN PROGRESS** |

Statuses change only when the corresponding review gate is completed in GitHub. API Contract v1 was merged in [`freight-docs#4`](https://github.com/isadulla7/freight-docs/pull/4), Backend Foundation 3.1A in [`freight-backend#1`](https://github.com/isadulla7/freight-backend/pull/1), 3.1B in [`freight-backend#2`](https://github.com/isadulla7/freight-backend/pull/2), 3.2 in [`freight-backend#3`](https://github.com/isadulla7/freight-backend/pull/3), and 3.3A in [`freight-backend#4`](https://github.com/isadulla7/freight-backend/pull/4) at `d0e15f7839fb07eb866c09c114c12135859d72f0`. Checkpoint 3.3B is the current review gate.
