# Project status

This file is a navigation summary. Accepted architecture remains in the [canonical context](architecture/architecture-context-v1.md) and [ADRs](docs/architecture/decisions/README.md).

## Current state

| Field | Value |
| --- | --- |
| Current architecture version | `v1.0` |
| Authoritative `main` | Resolve the current SHA from GitHub; repository state is authoritative |
| Latest completed architecture gate | Architecture v1.0 Lock |
| Current task | Phase 12 closure complete; international freight readiness enhancements in progress |
| Last completed backend checkpoint | `[12.10] SpringDoc OpenAPI with contract tests — MERGED` |
| Next task | Define the next main roadmap phase; finish the remaining international freight readiness phases separately |

## Roadmap

| Phase | Checkpoint | Status |
| ---: | --- | --- |
| 0 | Canonical Context | **COMPLETE** |
| 1 | Architecture v1.0 Lock | **COMPLETE** |
| 1.5 | Agent Handoff System | **COMPLETE** |
| 2 | API Contract v1 | **COMPLETE** |
| 3 | Backend Foundation | **COMPLETE** |
| 4 | Local Infrastructure | **COMPLETE** |
| 5 | Identity/Auth | **COMPLETE** |
| 6 | Accounts | **COMPLETE** |
| 7 | Fleet | **COMPLETE** |
| 8 | Freight | **COMPLETE** |
| 9 | Marketplace | **COMPLETE** |
| 10 | Shipment | **COMPLETE** |
| 11 | Communication | **COMPLETE** |
| 12 | API delivery layer (Security, REST, WebSocket, OpenAPI) | **COMPLETE** |

## Phase 12 checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 12.1 | JWT authentication filter (EdDSA) | **COMPLETE** |
| 12.2 | Auth API controllers (OTP, refresh, logout, sessions) | **COMPLETE** |
| 12.3 | User and company API controllers | **COMPLETE** |
| 12.4 | Vehicle and reference API controllers | **COMPLETE** |
| 12.5 | Load API controller | **COMPLETE** |
| 12.6 | Offer API controller | **COMPLETE** |
| 12.7 | Shipment API controller | **COMPLETE** |
| 12.8 | Conversation and notification API controllers | **COMPLETE** |
| 12.9 | STOMP WebSocket with JWT handshake | **COMPLETE** |
| 12.10 | SpringDoc OpenAPI with contract tests | **COMPLETE** |

## Post-Phase 12 maintenance

- International freight readiness work added structured load-stop locations, nullable validated currencies, freight documents, and initial locale persistence/API support.
- Main Kotlin packages were reorganized into explicit `api`, `application`, `domain/model`, `infrastructure/persistence`, `bootstrap`, and `shared` layers.
- Spring Modulith boundaries and public API surface tests were updated for the layered package structure.

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
| 5.7 | JWT access-token infrastructure (EdDSA/Ed25519, ADR-0016) | **COMPLETE** |
| 5.8 | ResolveAuthenticatedPrincipal + AuthenticatedPrincipal | **COMPLETE** |
| 5.9 | VerifyOtpAndRegister (wired after accounts.ProvisionUser) | **COMPLETE** |

## Accounts checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 6.1 | Accounts persistence foundation (Flyway V3 + 13 tables + JPA entities/repositories) | **COMPLETE** |
| 6.2 | ProvisionUser + VerifyOtpAndRegister cross-module wiring | **COMPLETE** |
| 6.3 | Remaining accounts services (GetUserSummary, RecordConsent, CreateOrUpdateDriverProfile, CreateCompany, AddCompanyMember, RemoveCompanyMember, AssignUserRole, AssignCompanyMemberRole, Authorize, GetDriverEligibility, AppendSecurityAudit) | **COMPLETE** |

## Fleet checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 7.1 | Fleet persistence foundation (Flyway V4 + 8 tables + PostGIS + JPA entities) | **COMPLETE** |
| 7.2 | Fleet services (CreateVehicle, PublishAvailableVehicle, SearchNearbyVehicles, ValidateVehicleEligibility) | **COMPLETE** |

## Freight checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 8.1 | Freight persistence foundation (Flyway V5 + 5 tables + PostGIS + JPA entities) | **COMPLETE** |
| 8.2 | Load lifecycle services (CreateLoad, UpdateDraftLoad, PublishLoad, CancelLoad, ExpireLoad, MatchLoad, GetLoadSummary, SearchLoads, ValidateOfferEligibility) | **COMPLETE** |

## Marketplace checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 9.1 | Marketplace persistence + full CQRS API (V6 migration, Offer entity, CreateOffer, WithdrawOffer, RejectOffer, AcceptOffer, GetOfferSummary, ListOffersForLoad, concurrency-safe winner selection) | **COMPLETE** |

## Shipment checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 10.1 | Shipment persistence + full CQRS API (V7 migration, Shipment/ShipmentStatusHistory entities, CreateShipment, GetShipment, ListUserShipments, UpdateShipmentStatus with state machine, GetShipmentStatusHistory) | **COMPLETE** |

## Communication checkpoints

| Checkpoint | Scope | Status |
| --- | --- | --- |
| 11.1 | Communication persistence + full CQRS API (V8 migration, 7 tables, 7 entities, GetOrCreateShipmentConversation, SendMessage, ListMessages, MarkConversationRead, ListNotifications, MarkNotificationRead, UpdateCommunicationPreferences, RegisterPushEndpoint) | **COMPLETE** |
