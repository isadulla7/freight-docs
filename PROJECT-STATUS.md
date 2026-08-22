# Project status

This file is a navigation summary. Accepted architecture remains in the [canonical context](architecture/architecture-context-v1.md) and [ADRs](docs/architecture/decisions/README.md).

## Current state

| Field | Value |
| --- | --- |
| Current architecture version | `v1.0` |
| Authoritative `main` | Resolve the current SHA from GitHub; repository state is authoritative |
| Latest completed architecture gate | Architecture v1.0 Lock |
| Current task | `[5] Identity/Auth — all accounts-independent public API COMPLETE; branch pushed, PR pending` |
| Last completed backend checkpoint | `[5.8] ResolveAuthenticatedPrincipal + JWT access-token infrastructure — PUSHED` |
| Next task | Create PR for `feat/identity-access-token-infrastructure`, merge, then start Phase 6 (accounts) or identity HTTP endpoints |

## Roadmap

| Phase | Checkpoint | Status |
| ---: | --- | --- |
| 0 | Canonical Context | **COMPLETE** |
| 1 | Architecture v1.0 Lock | **COMPLETE** |
| 1.5 | Agent Handoff System | **COMPLETE** |
| 2 | API Contract v1 | **COMPLETE** |
| 3 | Backend Foundation | **COMPLETE** |
| 4 | Local Infrastructure | **COMPLETE** |
| 5 | Identity/Auth | **IN PROGRESS (accounts-independent API complete, PR pending merge)** |
| 6 | Accounts | **PENDING** |
| 7 | Fleet | **PENDING** |
| 8 | Freight | **PENDING** |
| 9 | Marketplace | **PENDING** |
| 10 | Shipment | **PENDING** |
| 11 | Communication | **PENDING** |
| 12 | Mobile/Web | **PENDING** |

## Backend Foundation checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 3.1A | Kotlin/Spring Boot project scaffold | **COMPLETE** |
| 3.1B | Spring Modulith boundaries + architecture tests | **COMPLETE** |
| 3.2 | Security and configuration foundation | **COMPLETE** |
| 3.3A | PostgreSQL/PostGIS/Flyway/JPA/jOOQ foundation | **COMPLETE** |
| 3.3B | PostgreSQL/PostGIS integration validation with Testcontainers | **COMPLETE** |
| 3.4A | CI foundation | **COMPLETE** |
| 3.4B | Redis foundation | **COMPLETE** |
| 3.4C | API error contract foundation | **COMPLETE** |
| 3.4D | Backend Foundation closure gate | **COMPLETE** |

## Identity/Auth checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 5.1 | Identity persistence foundation (Flyway + JPA entities for auth_identities/auth_devices/auth_sessions) | **COMPLETE** |
| 5.2 | OTP challenge and rate-limit Redis primitives (OtpChallengeStore, OtpRateLimiter) | **COMPLETE** |
| 5.3 | Session lifecycle domain service (SessionLifecycleService: issue/refresh/revoke/list) | **COMPLETE** |
| 5.4 | Authenticate public application API | **COMPLETE** |
| 5.5 | RefreshSession/RevokeSession/RevokeAllUserSessions/ListUserSessions public API | **COMPLETE** |
| 5.6 | Public API surface lock-down (closure gate) | **COMPLETE** |
| 5.7 | JWT access-token infrastructure (EdDSA/Ed25519, ADR-0016) | **PUSHED** |
| 5.8 | ResolveAuthenticatedPrincipal + AuthenticatedPrincipal | **PUSHED** |
| — | `VerifyOtpAndRegister` | **BLOCKED** on `accounts.ProvisionUser` (Phase 6) |
