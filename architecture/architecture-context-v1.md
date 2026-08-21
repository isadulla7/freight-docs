# Freight Platform — Architecture Context v1.0

> **Purpose:** This document is the canonical handoff context for Codex and all future contributors.
> It captures the product vision, MVP scope, architecture, engineering principles, repository strategy,
> privacy/security requirements, performance expectations, and expansion path agreed for the project.
>
> **Rule:** Do not silently reinterpret or replace these decisions. If a future change is needed, record
> it explicitly as an ADR and update this document or its successor.

---

# 1. Product Vision

Build a real, production-oriented freight marketplace for Uzbekistan.

The platform connects:

- cargo owners (individuals),
- companies/organizations,
- truck drivers / carriers,
- vehicles,
- future enterprise logistics operators.

Core product idea:

1. A cargo owner or company creates a load.
2. The load has pickup and delivery locations.
3. Drivers/carriers can discover relevant loads.
4. Drivers submit price offers.
5. The cargo owner chooses an offer.
6. The accepted offer creates a real Shipment.
7. Shipment status is tracked through a lifecycle.
8. Both parties can rate each other after completion.

The product must not be treated as a simple classifieds app.
Its long-term value is:

- trust,
- fast matching,
- pricing/offer marketplace,
- logistics process management,
- shipment traceability,
- enterprise logistics workflows.

Positioning:

> A digital freight marketplace for Uzbekistan.

Long-term business direction may include:
- marketplace commission,
- enterprise SaaS,
- subscriptions,
- escrow,
- payment services,
- logistics analytics,
- enterprise fleet/logistics management.

---

# 2. Target Users

## 2.1 Individual cargo owner
A person who needs cargo moved from one location to another.

## 2.2 Company / organization
A business that creates freight requests and may have multiple staff/operators.

Potential future company roles:
- Owner
- Manager
- Dispatcher
- Accountant
- Operator
- Support/Operations roles as needed

## 2.3 Driver / carrier
A driver or transport provider with one or more vehicles.

Driver profile may contain:
- full name,
- phone number,
- driver verification state,
- driving licence data,
- vehicle ownership/association,
- ratings,
- shipment history.

## 2.4 Future enterprise operators
Large organizations with:
- multiple employees,
- permissions,
- reporting,
- accounting,
- fleet visibility,
- shipment analytics.

---

# 3. Marketplace Model

The platform is intentionally two-sided.

## 3.1 Load listing
Cargo owner/company posts:

- pickup location,
- delivery location,
- cargo type,
- weight,
- volume,
- quantity/pallets if relevant,
- cargo images,
- required vehicle type/body,
- pickup date/time,
- offered price or “wait for offers”.

Pricing modes:

- fixed price,
- receive offers / bidding.

Example:

Tashkent -> Bukhara\
8 tons construction materials\
Tent required\
Pickup date\
Customer price: 3,000,000 UZS

Drivers may offer:
- 2,800,000
- 2,950,000
- 3,100,000

Customer selects one.

## 3.2 Available vehicle listing
Driver may also advertise an empty/available vehicle.

Example:

MAN 20t\
Current location: Samarkand\
Direction: Tashkent\
Available date\
Body: Tent

This enables:

Load -> finds vehicle\
Vehicle -> finds load

This must remain a first-class product concept, not an afterthought.

---

# 4. MVP Philosophy

The MVP must be intentionally small in features but strong in foundations.

Core principle:

> Fewer features, correct architecture.

Do NOT build 15–50 features just because they may be useful later.

Build the minimum valuable set while getting these foundations correct from day one:

- database model,
- authentication,
- API contract,
- permissions,
- location/geospatial data,
- lifecycle/status state machines,
- module boundaries,
- privacy/security,
- documentation,
- observability baseline.

The system should be future-ready without premature abstraction.

---

# 5. MVP Feature Scope

Initial MVP should focus on approximately 7–15 essential capabilities.

Core candidates:

1. Phone-based registration/login via OTP
2. User/account roles
3. Driver profile
4. Vehicle creation and management
5. Load creation
6. Pickup/delivery location on map
7. Load feed
8. Filters/search
9. Driver price offers
10. Offer acceptance
11. Shipment creation and status lifecycle
12. Chat
13. Ratings
14. Push notifications
15. Admin/operations foundation

Not required in first MVP:
- payments,
- escrow,
- AI matching,
- advanced accounting,
- full live GPS tracking,
- advanced enterprise reporting,
- complex microservices,
- Kubernetes,
- Kafka.

These may be added later without rewriting the core.

---

# 6. Product Features Planned for Later

Possible future capabilities:

- live driver GPS tracking,
- payment provider integration,
- escrow,
- commissions,
- AI-assisted matching,
- favorite routes,
- route notifications,
- Telegram integration,
- enterprise dashboard,
- company multi-user administration,
- analytics,
- invoices,
- driver verification workflows,
- dispute management,
- blacklist/reporting,
- advanced moderation,
- subscriptions,
- international freight,
- multi-currency,
- partner APIs.

Architecture must allow extension, not require rewrite.

---

# 7. Architecture Strategy

## 7.1 Primary architecture

Use:

- **Modular Monolith**
- **DDD-lite**
- **Hexagonal Architecture**
- **CQRS-lite**
- **Spring Modulith**

Do NOT start with microservices.

Reason:
- MVP speed,
- simpler operations,
- lower infrastructure cost,
- easier debugging,
- simpler transactions,
- easier deployment,
- fewer distributed-system problems.

The code must still have strict internal module boundaries so future extraction to services remains practical.

---

# 8. Backend Technology Stack

Final backend direction:

- Language: **Kotlin**
- JVM runtime: **Java 25 LTS**
- Framework: **Spring Boot**
- Build system: **Gradle Kotlin DSL**
- Module boundary enforcement: **Spring Modulith**
- Security: **Spring Security**
- Primary database: **PostgreSQL**
- Geospatial: **PostGIS**
- Transactional persistence: **Spring Data JPA / Hibernate**
- Complex reads/feed/search/reporting: **jOOQ**
- Migrations: **Flyway**
- Cache/transient state: **Redis**
- API style: **REST**
- API specification: **OpenAPI**
- Realtime: **WebSocket only where actually needed**
- Testing: **JUnit + Testcontainers**
- Containerization: **Docker**
- Local environment: **Docker Compose**
- Object storage: **S3-compatible abstraction**
- Local object storage: **MinIO**
- Push: provider abstraction, initial likely Firebase Cloud Messaging
- External providers: adapter pattern

Spring Boot remains the backend framework.

---

# 9. Why Kotlin + Spring Boot

Use the JVM ecosystem while keeping code concise and modern.

Kotlin is preferred because of:
- null-safety,
- concise models,
- data classes,
- extension support,
- good Java interoperability,
- mature Spring support.

Important:

Kotlin/Spring must not lead to framework leakage into domain logic.

The domain should not depend on:
- Spring MVC,
- JPA annotations,
- Redis clients,
- Firebase,
- Maps SDKs,
- S3 SDKs,
- SMS SDKs.

---

# 10. Backend Module Boundaries

Initial modules:

- `identity`
- `accounts`
- `fleet`
- `freight`
- `marketplace`
- `shipment`
- `communication`

## 10.1 identity
Owns:
- OTP
- login
- sessions
- refresh tokens
- access token lifecycle
- device sessions
- authentication-related security

Does NOT own:
- user business profile,
- vehicles,
- loads.

## 10.2 accounts
Owns:
- User
- DriverProfile
- Company
- CompanyMember
- roles
- permissions
- profile verification state

## 10.3 fleet
Owns:
- Vehicle
- vehicle type
- body type
- capacity
- vehicle documents
- verification state
- AvailableVehicle

## 10.4 freight
Owns:
- Load
- Cargo
- LoadStop
- cargo images
- load requirements
- load pricing intent
- load lifecycle

## 10.5 marketplace
Owns:
- Offer/Bid
- offer lifecycle
- pricing proposals
- matching coordination
- future matching engine boundary

## 10.6 shipment
Owns:
- Shipment
- assignment
- shipment lifecycle
- pickup/delivery execution
- shipment status history

## 10.7 communication
Owns:
- Chat
- Message
- notifications
- push orchestration
- SMS orchestration
- communication preferences

---

# 11. Module Dependency Rules

These rules are mandatory.

- No cyclic module dependencies.
- A module must not access another module's internal repository.
- A module must not access another module's persistence entity.
- Cross-module coordination must happen via:
  - public application API, or
  - domain/application events.
- `shared` must remain tiny.
- `shared` is not a dumping ground.
- Business logic stays in domain/application layers.
- Controllers contain no real business logic.
- Persistence adapters contain no business decisions.

Spring Modulith tests should enforce boundaries.

If a developer imports another module's internal implementation, CI should fail.

---

# 12. Internal Module Structure

Typical module shape:

```text
module/
├── domain/
│   ├── model/
│   ├── valueobject/
│   ├── event/
│   ├── service/
│   └── repository/
│
├── application/
│   ├── command/
│   ├── query/
│   ├── usecase/
│   └── port/
│
├── adapter/
│   ├── in/
│   │   ├── rest/
│   │   └── websocket/
│   └── out/
│       ├── persistence/
│       ├── maps/
│       ├── storage/
│       └── providers/
│
└── config/
```

Use pragmatism:
- strict boundaries for core business domains,
- avoid unnecessary ceremony for simple reference/configuration data.

---

# 13. Avoid Framework-Coupled Domain Models

Core domain models should not automatically be JPA entities.

Preferred approach for important aggregates:

```text
Domain Model
    ↕ mapper
Persistence Entity
```

Example:

Domain:
- `Load`

Persistence:
- `LoadJpaEntity`

Use this strongly where business logic matters.

For trivial reference tables, avoid excessive boilerplate if it provides no value.

Guiding principle:

> Strong boundaries without architecture cosplay.

---

# 14. CQRS-lite Persistence Strategy

Use one PostgreSQL database.

## Write side
Use:
- JPA/Hibernate

Good for:
- CreateLoad
- AcceptOffer
- AssignDriver
- UpdateShipmentStatus
- transactional aggregate updates

## Read side
Use:
- jOOQ

Good for:
- load feed
- nearby loads
- filters
- search
- company dashboards
- driver history
- reports
- analytics

Do NOT create separate databases or a distributed CQRS system for MVP.

---

# 15. PostgreSQL + PostGIS

PostgreSQL is the primary source of truth.

PostGIS is required from day one because the business is location-heavy.

Use proper geospatial fields/indexes for:
- pickup points,
- delivery points,
- driver/vehicle current availability location,
- nearby load search,
- radius matching.

Avoid:
- loading all records to application memory,
- calculating all distances in Dart/Kotlin,
- filtering location results on the client.

Use spatial database queries and GIST indexes.

Example use case:
- driver is in Tashkent,
- search loads within 30/50/100 km,
- filter by vehicle type,
- weight,
- date,
- direction,
- status.

The database should return only relevant results.

---

# 16. Load Data Model

Do NOT model a load as only `from` and `to`.

Use:

- `loads`
- `load_stops`

Reason:
future multi-stop routes must be possible.

`load_stops` concept:
- id
- load_id
- type
- sequence
- address
- latitude
- longitude
- geospatial location
- contact name
- contact phone
- scheduled time

Example:

Stop 1: Tashkent — PICKUP\
Stop 2: Jizzakh — intermediate\
Stop 3: Samarkand — DELIVERY

This avoids future schema rewrite.

---

# 17. User and Role Model

Use a stable `users` entity.

User types must not be hardcoded with fragile numeric checks like:

```text
if user.type == 1
```

Use roles/permissions.

A user may eventually have multiple capabilities:
- DRIVER
- CARGO_OWNER
- COMPANY_OPERATOR

Permissions should be capability-based, e.g.:

- LOAD_CREATE
- LOAD_EDIT
- LOAD_DELETE
- OFFER_CREATE
- OFFER_ACCEPT
- VEHICLE_CREATE
- COMPANY_MEMBER_ADD
- DOCUMENT_VERIFY
- etc.

Authorization decisions must be server-side.

---

# 18. Company Model

From day one support:

- companies
- company_members

A company may eventually have many users.

Example:
- Owner
- Manager
- Dispatcher
- Accountant

Do not tie a company to a single user.

---

# 19. Vehicle Model

Vehicle includes:

- id
- owner/manager relation
- brand
- model
- year
- plate number
- vehicle type
- body type
- capacity kg
- volume m3
- verification status
- active flag

Avoid hardcoding vehicle types only in mobile/web code.

Reference/config data should be backend-driven where appropriate.

---

# 20. Available Vehicle

Keep AvailableVehicle distinct from Vehicle.

A Vehicle is a persistent asset.
An AvailableVehicle is an availability listing:

- vehicle,
- current location,
- desired direction,
- date/time,
- availability window.

This is important for the two-sided marketplace.

---

# 21. Offer Model

Offer fields may include:

- id
- load_id
- driver/carrier_id
- vehicle_id
- amount
- currency
- optional message
- status
- created_at
- updated_at

Offer statuses:

- PENDING
- ACCEPTED
- REJECTED
- WITHDRAWN
- EXPIRED

Only one offer may win a load.

Offer acceptance must be protected against concurrency/race conditions.

Use:
- transactions,
- optimistic locking/versioning where appropriate,
- unique constraints where appropriate.

---

# 22. Load != Shipment

This is a critical architectural rule.

A `Load` is a marketplace listing/request.

A `Shipment` is the actual transport execution after an offer is accepted.

Flow:

Load\
-> Offers\
-> Offer Accepted\
-> Shipment Created

Never merge the two concepts just because MVP is small.

---

# 23. Shipment Lifecycle

Initial lifecycle:

- CREATED
- DRIVER_ASSIGNED
- HEADING_TO_PICKUP
- AT_PICKUP
- LOADED
- IN_TRANSIT
- DELIVERED
- COMPLETED

Alternative cancellation/dispute flows will be specified before implementation.

Invalid transitions must be blocked in backend domain logic.

Example:
- CREATED -> DELIVERED is invalid.

Frontend must not be responsible for enforcing lifecycle correctness.

---

# 24. Status History

Do not only overwrite `shipment.status`.

Maintain immutable status history.

Store:
- shipment_id
- previous_status
- new_status
- actor
- timestamp
- optional reason/context

This supports:
- audit,
- disputes,
- support,
- analytics,
- SLA measurement,
- operational visibility.

---

# 25. Event-Driven Internal Architecture

Use events for cross-cutting reactions.

Example:

OfferAccepted
-> create shipment
-> notify participants
-> analytics/audit updates

Do NOT start with Kafka/RabbitMQ unless real requirements appear.

Initially use:
- Spring application events,
- Spring Modulith event publication / reliable event patterns as appropriate.

Later, if justified:
- Kafka can replace the transport mechanism.

Business logic must not depend directly on Kafka.

---

# 26. REST vs WebSocket

Use REST for normal application operations:

- auth,
- profile,
- load CRUD,
- vehicle CRUD,
- search,
- offers,
- shipment queries.

Use WebSocket only where realtime is beneficial:

- chat messages,
- new offer notification,
- offer accepted,
- live shipment status updates,
- future live driver location.

Do not make everything WebSocket-based.

---

# 27. API Contract

Base path:

`/api/v1`

OpenAPI is the contract source of truth.

Generate typed clients for:
- Flutter/Dart
- Next.js/TypeScript

Goal:
backend contract changes should surface early in builds/tests rather than becoming runtime surprises.

Initial API groups:

- /auth
- /users
- /companies
- /vehicles
- /loads
- /offers
- /shipments
- /chat
- /notifications

Use stable machine-readable error formats.

Use idempotency where future financial or high-risk mutation flows require it.

---

# 28. Commands and Queries

Use explicit application use cases.

Examples:

Commands:
- CreateLoadCommand
- PublishLoadCommand
- CancelLoadCommand
- CreateOfferCommand
- AcceptOfferCommand
- StartShipmentCommand
- CompleteShipmentCommand

Queries:
- GetLoadQuery
- SearchLoadsQuery
- GetLoadFeedQuery
- GetShipmentQuery

Do not add a heavy CQRS framework.

Simple Kotlin application classes are sufficient.

---

# 29. Controllers

Controllers must remain thin.

They should:
- validate/translate HTTP input,
- invoke an application use case,
- translate result to HTTP response.

They must NOT:
- contain business rules,
- send notifications directly,
- calculate routes directly,
- update unrelated repositories,
- implement permission logic in ad hoc ways.

---

# 30. Code Growth / Anti-Bloat Rules

The codebase must not “swell” into unnecessary complexity.

Mandatory principles:

- minimal complexity,
- strict boundaries,
- no premature abstraction,
- no generic abstraction unless duplication/problem is real,
- no 1000-line service classes,
- no all-purpose `Utils`,
- no giant `BaseService`,
- no “future-proof” classes for features that do not exist,
- no pattern just because it is fashionable.

Preferred:

- small explicit use cases,
- clear module ownership,
- readable code,
- feature locality.

Example:

Bad:
`LoadService` does create, delete, SMS, route calculation, offer acceptance, shipment creation, uploads, notifications.

Good:
- CreateLoadUseCase
- UpdateLoadUseCase
- CancelLoadUseCase
- CreateOfferUseCase
- AcceptOfferUseCase
- CreateShipmentUseCase
- CompleteShipmentUseCase

Guideline:

> A new developer should be able to understand where a feature lives within roughly 10 minutes.

---

# 31. Feature Flags and Dynamic Configuration

Avoid hardcoding business configuration where it is likely to change.

Future feature flags may include:

- payments
- liveTracking
- companyAccounts
- telegramIntegration

Remote/business configuration may include:

- maxVehicleImages
- maxLoadImages
- offerExpirationHours
- nearbyLoadRadiusKm

Do not build a huge feature-flag platform now.
Keep an abstraction/path that allows configuration to be server-controlled where useful.

---

# 32. Provider Adapter Strategy

External services must be abstracted behind ports/adapters.

Examples:

## SMS
`SmsPort`
- local mock/log adapter
- Uzbekistan SMS provider adapter(s)

## Maps
`MapProvider`
- Google adapter
- Yandex adapter
- future provider

Domain uses:
- GeoPoint
- Address
- RouteDistance
- RouteDuration

Domain must not use provider-specific response classes.

## Object Storage
`FileStorage`
- MinIO local adapter
- S3-compatible production adapter

## Push
`PushNotificationPort`
- Firebase adapter initially if chosen

## Payments (future)
`PaymentPort`
- Click
- Payme
- Uzum
- others

Do not implement full payment architecture before payments exist.

---

# 33. Money

Never use floating point for money.

Store:
- amount as integer/minor unit or safe integer representation
- currency as ISO-style code

Example:
- amount = 3,000,000
- currency = UZS

Future currencies may include:
- USD
- KZT
- RUB

---

# 34. Time

Store server timestamps in UTC.

Convert for users based on timezone.

Initial primary timezone:
- Asia/Tashkent

This is required for future international expansion.

---

# 35. IDs

Use UUIDs for public/domain identifiers.

Avoid sequential public IDs as the primary external identifier.

---

# 36. Feed and Search Performance

Performance is an architectural requirement.

Key rules:

- cursor pagination,
- infinite scrolling on clients,
- server-side filtering,
- geospatial filtering in PostgreSQL/PostGIS,
- correct indexes,
- small payloads,
- avoid N+1 queries,
- optimized read models,
- Redis only for hot/transient data.

Example feed request:
- driver location,
- radius,
- vehicle type,
- capacity,
- date,
- load status,
- optional route/direction filters.

Return only top relevant records, not the entire table.

---

# 37. Pagination

Prefer cursor pagination for large feeds.

Example:
`GET /api/v1/loads?cursor=...&limit=20`

Avoid offset pagination for very large/high-churn feeds unless the use case specifically benefits from it.

---

# 38. Redis

Redis is NOT the primary database.

Use Redis for:
- OTP,
- rate limiting,
- hot cache,
- session/token metadata where appropriate,
- online presence,
- temporary realtime state,
- queue support where appropriate.

Primary truth remains PostgreSQL.

---

# 39. Search Engine

Do NOT introduce Elasticsearch/OpenSearch in MVP.

PostgreSQL + PostGIS should handle initial search/filter needs.

Only add a dedicated search engine when scale/feature complexity actually justifies it.

---

# 40. Mobile Technology

Mobile:
- Flutter
- Android
- iOS

Flutter Web is NOT the primary web strategy.

State management:
- Riverpod

Routing:
- go_router

Architecture:
feature-first.

Typical shape:

```text
lib/
├── app/
│   ├── bootstrap/
│   ├── routing/
│   └── theme/
├── core/
│   ├── network/
│   ├── storage/
│   ├── auth/
│   ├── location/
│   └── realtime/
└── features/
    ├── auth/
    ├── home/
    ├── loads/
    ├── offers/
    ├── fleet/
    ├── shipments/
    ├── chat/
    └── profile/
```

UI must not call HTTP clients directly.

Preferred flow:

Screen
-> Controller/ViewModel
-> Repository/Use Case
-> Generated/API client

---

# 41. Web Technology

Web:
- Next.js
- React
- TypeScript

Use web for:
- customer/company portal,
- desktop-heavy workflows,
- future enterprise dashboards,
- operations/admin tools where appropriate.

Web and mobile must be independent clients over the same backend contract.

---

# 42. Repository Strategy

Separate repositories:

- `freight-backend`
- `freight-mobile`
- `freight-web`
- `freight-infrastructure`
- `freight-contracts`
- `freight-docs`

Current known repo:
- `freight-docs` already created and pushed to GitHub.

Do not merge backend, mobile, web, and infrastructure into one giant repo unless a future ADR explicitly changes this decision.

---

# 43. Branching Strategy

Prefer:
- `main`
- short-lived `feature/*`
- short-lived `fix/*`
- Pull Requests

Do NOT keep a permanent `develop` branch unless the team later has a real release-management need.

`main` should remain deployable/healthy.

---

# 44. Documentation as Code

Documentation is part of engineering output.

Keep in Git:
- architecture docs,
- ADRs,
- domain diagrams,
- API rules,
- privacy documentation,
- security rules,
- operations runbooks,
- deployment docs,
- backup/restore docs,
- data inventory.

Every meaningful architecture change should be:
1. discussed,
2. recorded as ADR,
3. reflected in implementation.

---

# 45. ADR Policy

Architecture Decision Records should capture important choices.

Existing/expected ADR themes:

- Kotlin + Spring Boot
- Modular Monolith
- PostgreSQL + PostGIS
- JPA + jOOQ
- Gradle Kotlin DSL
- OpenAPI contract
- Spring Modulith boundaries
- Docker Compose local environment
- frontend choices
- privacy/security decisions
- hosting/deployment evolution

---

# 46. Local Development

Goal:

A developer should be able to start infrastructure locally with a simple command.

Use Docker Compose for:
- PostgreSQL + PostGIS
- Redis
- MinIO
- future local mocks/emulators where useful

Example local endpoints may be:
- backend: localhost:8080
- web: localhost:3000

Backend can run:
- from IDE during development,
- or fully containerized under a compose profile.

Flutter runs from IDE/device/emulator.

---

# 47. Local -> Production Principle

Build the system so local and production differ mostly by configuration, not by application logic.

Configuration comes from:
- environment variables,
- secret manager in production where appropriate.

Never hardcode:
- database hosts,
- provider endpoints,
- credentials,
- tokens,
- production URLs.

Example configuration names:
- DB_HOST
- DB_PORT
- DB_NAME
- REDIS_HOST
- S3_ENDPOINT
- S3_BUCKET
- JWT_PRIVATE_KEY
- SMS_PROVIDER
- MAPS_PROVIDER
- FRONTEND_URL

---

# 48. Production Deployment Evolution

Initial production should be simple.

Do NOT start with Kubernetes.

Initial direction:
- Linux VPS or cloud VM
- Docker
- Docker Compose
- reverse proxy
- HTTPS
- API
- web
- Redis
- S3-compatible storage
- PostgreSQL/PostGIS

Prefer managed PostgreSQL when budget allows.

If self-hosted PostgreSQL is used initially:
- automated backups are mandatory,
- restore process must be tested.

Later scale:
- multiple stateless API instances behind a load balancer,
- shared PostgreSQL,
- shared Redis.

Only extract microservices when real scaling/team/operational reasons appear.

---

# 49. Testing

Use:
- JUnit
- Testcontainers

Do NOT rely on H2 as a replacement for PostgreSQL for integration tests.

Reason:
- PostGIS,
- PostgreSQL-specific behavior,
- jOOQ queries,
- indexes,
- SQL semantics.

Integration tests should run against real PostgreSQL-compatible containers.

---

# 50. Architecture Tests

CI should enforce module architecture.

Examples:
- no cyclic dependencies,
- no imports into another module’s internals,
- module boundaries stay intact.

Spring Modulith verification should be part of CI.

Architecture must be executable/enforced, not just drawn in diagrams.

---

# 51. CI/CD

Planned:
- GitHub Actions

CI should eventually run:
- build
- lint/static analysis
- unit tests
- integration tests
- architecture tests
- migration checks
- contract checks
- secret scanning where appropriate

No push should silently bypass critical validation in normal workflow.

---

# 52. Static Analysis / Quality

The project should include suitable JVM and frontend/mobile quality tools.

Goals:
- detect unused code,
- suspicious patterns,
- style inconsistency,
- overly complex code,
- architecture violations,
- obvious bugs.

Do not overconfigure linters to the point they slow development without value.

---

# 53. Privacy by Design

Privacy is part of architecture from the beginning.

The platform may process:
- phone numbers,
- names,
- company information,
- vehicle information,
- license/verification documents,
- pickup/delivery locations,
- live location,
- chat,
- shipment history,
- future payment-related metadata.

For every new personal-data field or feature answer:

1. What is collected?
2. Why is it collected?
3. Where is it stored?
4. Who can access it?
5. How long is it retained?
6. How is access audited?
7. How is it deleted/anonymized when no longer needed?

---

# 54. Data Minimization

Do not collect data just because it may be useful someday.

Example:
registration should not require passport/license/home address before the actual business flow needs verification.

Basic account may start with:
- phone,
- name.

Verification data is requested later when required.

---

# 55. Privacy Data Categories

Maintain a data inventory at minimum for:

## Identity
- name
- phone
- user id

## Business
- company data
- tax/business identifiers
- roles

## Vehicle
- plate
- type
- capacity
- documents

## Location
- pickup
- delivery
- current GPS
- live shipment GPS

## Communication
- chat messages

## Verification
- driver licence
- identity documents

## Financial (future)
- transaction id
- payment status
- invoice data

Each category may have different:
- access,
- retention,
- deletion,
- audit requirements.

---

# 56. Sensitive Document Storage

Documents such as identity or driving licence images must NOT be public.

Use:
- private object storage,
- authorization check,
- short-lived signed URLs where appropriate,
- audit access for privileged operations.

Never expose permanent public URLs for sensitive documents.

---

# 57. Location Privacy

Location has separate purposes.

Do not treat all location access as one blanket permission.

Examples:

## Nearby load discovery
Purpose:
- show relevant loads.

## Live shipment tracking
Purpose:
- show shipment progress to authorized participants.

These should have separate privacy/consent reasoning.

Do not collect continuous location before it is necessary.

---

# 58. Consent

Consent must be auditable/versioned.

Potential record:

- user_id
- consent_type
- policy_version
- accepted_at
- withdrawn_at

Examples:
- PRIVACY_POLICY v1.x
- LOCATION_TRACKING v1.x

UI checkbox alone is not sufficient evidence.

---

# 59. Account Deletion

Do not assume account deletion equals:

`DELETE FROM users`

The system may need to retain some historical shipment/business records.

Define per category:
- hard delete,
- anonymization,
- retention,
- legal/business preservation.

This must be reflected in schema and domain design.

---

# 60. Authorization / Least Privilege

Do not implement:
> admin = can see everything

Use scoped permissions.

Possible future admin roles:
- Support Admin
- Verification Admin
- Finance Admin
- Super Admin

Permissions should control access to sensitive resources separately.

Example:
- LOAD_READ does not imply DRIVER_DOCUMENT_READ.

---

# 61. Audit Logging

Audit sensitive operations such as:
- document views,
- permission changes,
- company membership changes,
- verification decisions,
- shipment status changes,
- account blocking,
- high-risk security events.

Audit records should capture:
- who,
- what,
- when,
- resource/context.

Do NOT log:
- passwords,
- raw tokens,
- unnecessary personal-data payloads,
- OTP values in production logs.

---

# 62. Security Baseline

MVP auth:
- phone + OTP
- short-lived access token
- refresh token/session lifecycle
- device sessions
- OTP rate limiting
- login abuse controls

Use:
- TLS in non-local environments,
- private secrets,
- no credentials in Git,
- `.env.example` with names only,
- encrypted backups where infrastructure supports it,
- tested restore procedures.

---

# 63. Privacy / Legal Documentation

Maintain documents for:
- privacy-by-design
- data inventory
- data flow
- retention policy
- consent model
- third-party processors
- Privacy Policy
- Terms of Service
- driver terms
- company terms
- cookie policy (if relevant)

Before public production launch, legal/privacy documents should be reviewed for Uzbekistan-specific compliance by a qualified local legal professional.

Do not treat this architecture document as legal advice.

---

# 64. Third-Party Provider Inventory

Track every third-party provider.

Potential examples:
- Firebase
- SMS provider
- Maps provider
- object storage/cloud
- analytics
- crash reporting
- payment provider

For each provider document:
- what data is sent,
- purpose,
- storage/processing region if relevant,
- retention if known,
- legal/compliance considerations.

---

# 65. Definition of Done

A feature is not complete just because “it works”.

Where applicable, completion includes:

- business behavior implemented
- tests
- architecture boundary checks
- migration
- API contract update
- permissions reviewed
- privacy purpose reviewed
- retention behavior reviewed
- audit behavior added if required
- logs/metrics added
- failure handling defined
- docs updated
- no secrets committed
- no unresolved TODO that affects correctness

For sensitive features add:
- consent review
- data access review
- deletion/anonymization behavior.

---

# 66. Performance Principles

Fast user experience is required.

Focus on:
- database indexes,
- spatial indexes,
- efficient SQL,
- jOOQ for read-heavy paths,
- avoiding N+1,
- cursor pagination,
- small response payloads,
- caching only hot data,
- correct connection pooling,
- background processing for slow non-critical tasks,
- lazy/infinite feed loading.

Do not waste time micro-optimizing Kotlin syntax while SQL/database design is poor.

---

# 67. Background Work

Asynchronous/non-blocking processing may be used for:
- SMS,
- push notifications,
- selected analytics,
- file processing,
- non-critical external provider calls.

The abstraction should allow future queue changes.

Do not introduce a distributed queue platform before needed.

---

# 68. Notifications

Notification reactions should be event-driven.

Example:
OfferCreated
-> push notification
-> WebSocket update
-> analytics

Offer business logic must not directly depend on Firebase.

---

# 69. Chat

Chat is planned as part of MVP or near-MVP.

Architecture:
- communication module owns chat/messages,
- WebSocket for realtime delivery,
- REST may still be used for history/pagination,
- phone numbers should not necessarily be exposed immediately.

Business concern:
if users immediately exchange phone numbers and bypass the platform, marketplace retention/monetization may suffer.

This product/business concern should be considered in UX and privacy design.

---

# 70. Ratings and Trust

After shipment completion:
- cargo owner can rate driver/carrier,
- driver/carrier can rate cargo owner/company.

Future trust signals:
- phone verified
- documents verified
- vehicle verified
- shipment count
- delivery success rate
- rating score

Trust is a core product value.

---

# 71. Matching

Matching is future strategic value.

Initial matching may be rule-based:
- location/radius,
- route/direction,
- vehicle type,
- capacity,
- date,
- status.

Later:
- ranking,
- historical behavior,
- acceptance probability,
- price intelligence,
- AI matching.

Keep matching within marketplace boundaries so it can evolve independently.

---

# 72. Favorite Routes / Notifications

Future useful feature:

Driver saves:
- Tashkent <-> Samarkand

When a matching load appears:
- push notification,
- possibly Telegram integration.

This should not affect MVP architecture beyond ensuring event/provider flexibility.

---

# 73. Telegram Integration

Telegram may become a strong acquisition/retention channel in Uzbekistan.

Potential flow:
Company creates load on web.
Driver receives Telegram notification.
Click opens mobile app/deep link.

Do not make Telegram a core dependency.
Treat it as another communication/integration adapter.

---

# 74. Maps

User experience should support:
- selecting pickup point on map,
- selecting delivery point on map,
- address search,
- route distance,
- route duration,
- future ETA.

Maps provider must be replaceable via adapter.

Do not hardwire business logic to Google/Yandex response structures.

---

# 75. Live Tracking

Not required in first MVP.

When introduced:
- separate purpose/consent,
- explicit shipment scope,
- access only to authorized participants,
- retention policy,
- operational limits,
- battery/network impact considered.

Do not store infinite GPS history by default.

---

# 76. Payments

Not required in MVP.

Future model may be:
customer pays platform
-> funds held
-> shipment delivered
-> customer confirms
-> carrier paid
-> platform takes commission

Potential providers:
- Click
- Payme
- Uzum
- others

Architecture must allow payment provider adapters later.

Do not implement full payment/escrow classes today just to be “future-proof”.

---

# 77. Enterprise SaaS Direction

Future company product may include:
- multiple dispatchers,
- role-based access,
- organization dashboard,
- shipment history,
- reports,
- accounting integration,
- operator activity,
- invoices,
- enterprise API.

This is why Company and CompanyMember must be modeled correctly from the start.

---

# 78. Initial Market Rollout Thinking

Marketplace liquidity is a key business risk.

Do not assume national coverage instantly.

Potential strategy:
start with active corridors such as:
- Tashkent -> Samarkand
- Tashkent -> Fergana Valley
- Tashkent -> Bukhara

Goal:
create dense supply/demand before expanding everywhere.

This is product strategy, not a hard technical constraint, but architecture should support region/route filtering.

---

# 79. Source of Truth Hierarchy

When implementing:

1. latest approved ADR
2. this architecture context / successor version
3. module documentation
4. OpenAPI contract
5. code/tests

If code and docs diverge, treat it as a defect and reconcile via explicit decision.

---

# 80. Expected Future Repository Responsibilities

## freight-backend
Kotlin/Spring code, migrations, tests, module boundaries.

## freight-mobile
Flutter Android/iOS client.

## freight-web
Next.js/React/TypeScript client.

## freight-infrastructure
Docker Compose, reverse proxy, environments, deployment, backups, monitoring.

## freight-contracts
OpenAPI artifacts and client generation configuration.

## freight-docs
Architecture, ADRs, privacy, security, operations, product-domain technical documentation.

---

# 81. Initial Commit Discipline

Prefer small, meaningful commits.

Examples:

- `docs: establish architecture baseline`
- `build: initialize kotlin spring backend`
- `infra: add local postgres postgis redis minio`
- `feat(identity): add otp authentication flow`
- `feat(freight): add load aggregate`
- `feat(marketplace): add offer acceptance`

Do not mix unrelated large changes in one commit.

---

# 82. GitHub / Codex Working Model

Codex may work directly with GitHub repositories.

Important:
ChatGPT conversation history is not automatically visible to Codex.

Therefore:
- this document is the canonical context handoff,
- Codex should read this file before architecture-impacting work,
- architecture-impacting changes should be recorded as ADRs,
- code should be committed/pushed incrementally.

---

# 83. Current Known GitHub State

`freight-docs` has been created and pushed.

Known details from the handoff:
- repository: `isadulla7/freight-docs`
- branch: `main`
- repository is currently public

Before adding sensitive internal operational details or secrets:
- review whether the repository should remain public,
- never commit credentials or secrets,
- consider making architecture/security operational details private if they become sensitive.

---

# 84. Immediate Next Steps

Do NOT jump directly into product feature coding before the baseline is clean.

Recommended order:

1. Commit this Architecture Context v1.0 into `freight-docs`.
2. Review existing docs for duplication/conflict and align them.
3. Add missing ADRs for:
   - frontend stack,
   - OpenAPI contract,
   - Spring Modulith,
   - privacy/security baseline,
   - repository strategy.
4. Finalize domain boundaries:
   - identity
   - accounts
   - fleet
   - freight
   - marketplace
   - shipment
   - communication
5. Finalize domain entities/aggregates.
6. Finalize state machines.
7. Create database ERD.
8. Define API conventions and initial OpenAPI skeleton.
9. Create `freight-backend`.
10. Create clean backend skeleton with architecture tests.
11. Add local infrastructure through `freight-infrastructure`.
12. Only then begin first real MVP feature.

---

# 85. Non-Negotiable Engineering Principles

These are the final high-level rules:

1. **MVP features stay small; foundations stay strong.**
2. **Modular monolith first.**
3. **No microservices without a real reason.**
4. **No premature abstraction.**
5. **Strict module boundaries.**
6. **Domain is independent of infrastructure/providers.**
7. **PostgreSQL is the source of truth.**
8. **PostGIS from day one.**
9. **JPA for transactional writes; jOOQ for complex reads.**
10. **OpenAPI is a real contract.**
11. **Flutter for mobile, Next.js/React/TypeScript for web.**
12. **Docker Compose local-first, production-compatible containers.**
13. **Privacy by design.**
14. **Security by default.**
15. **Least privilege.**
16. **Sensitive files are private.**
17. **Location collection is purpose-specific.**
18. **Documentation as code.**
19. **Architecture tests in CI.**
20. **Code must remain understandable and not bloat.**
21. **A feature is not done without tests/docs/security/privacy checks where applicable.**
22. **Future additions should be extensions, not rewrites.**

---

# 86. Instruction to Codex

Before making architecture-impacting changes:

- read this document,
- read relevant ADRs,
- inspect current repository state,
- preserve accepted decisions,
- do not introduce extra infrastructure/frameworks unless justified,
- prefer the simplest implementation consistent with future growth,
- update documentation when decisions change,
- create small commits,
- push only reviewed/intentional changes.

If a decision conflicts with this document:
- stop treating it as implicit,
- create or update an ADR,
- document why the change is needed,
- update the context after the decision is accepted.

This file should evolve as the project evolves.
