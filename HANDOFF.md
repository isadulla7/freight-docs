# Current handoff

| Field | Value |
| --- | --- |
| Repositories | `isadulla7/freight-docs`, `isadulla7/freight-backend`, `isadulla7/freight-mobile` |
| freight-docs branch | `claude/mobile-continuation-bc5tsr` |
| freight-backend branch | `claude/mobile-continuation-bc5tsr` |
| freight-mobile branch | `claude/mobile-continuation-bc5tsr` |
| Base branch | `main` (backend, docs), `master` (mobile) |
| Current phase | Local dev environment + E2E verification |
| Last completed work | Docker setup, seed data, full E2E flow verified |
| Next step | Continue mobile app features |

## Work state

- Phases 1–12 are COMPLETE and merged to `main`.
- Phase 11 (Communication): V8 migration, 7 tables, 7 entities, 8 services, 17 integration tests + public API surface test.
- Phase 12: JWT security, REST controllers for all business modules, STOMP WebSocket, and SpringDoc OpenAPI contract tests.
- Post-Phase 12: international freight enhancements and a layered package architecture refactor were merged.

### Mobile–backend alignment (merged to main)

Backend changes:
- OTP hardcoded to `123456` for development (`RequestOtp.kt`)
- `GET /companies/me` — `ListUserCompanies` use case + controller endpoint
- `GET /vehicles` — `ListUserVehicles` use case + controller endpoint
- `profileId` added to `DriverEligibilityResponse`
- New DTOs: `CompanyResponse`, `CompanyListResponse`, `VehicleListResponse`
- `CompanySummary.status` changed from `CompanyStatus` to `String` (fixes `internal` visibility leak)
- Dockerfile + docker-compose backend service added
- V15 seed reference data migration (roles, vehicle/body types, capabilities)

Mobile changes:
- `CompanyResponse` aligned: `legalName`, `displayName`, `businessIdentifier`
- `CreateCompanyPayload` simplified (removed address/phone)
- `CompanyRepository.getMyCompanies()` added
- `CompaniesScreen` fetches and displays real company data
- `DriverEligibilityResponse` now includes `profileId`
- `CreateOfferSheet` rewritten to fetch real driver profile and vehicle data

### E2E verification (completed)

Full flow tested locally with all 56 API endpoints available:
OTP → Auth → Profile → Role → Driver Profile → Vehicle → Company → Load → Publish → Offer
All steps pass. Swagger UI at `/swagger-ui/index.html`.

## GitHub state

| Field | Value |
| --- | --- |
| `freight-backend` main SHA | `433a67a` |
| `freight-backend` feature SHA | `c9c1625` |
| `freight-mobile` master SHA | See remote |
| `freight-mobile` feature SHA | `5e120a5` |
| Open PRs | None |

## Flyway state

| Version | Module | Tables |
| --- | --- | --- |
| V1 | schemas + PostGIS | 7 schemas created |
| V2 | identity | 3 tables |
| V3 | accounts | 13 tables |
| V4 | fleet | 8 tables |
| V5 | freight | 5 tables |
| V6 | marketplace | 1 table |
| V7 | shipment | 2 tables |
| V8 | communication | 7 tables |
| V9 | freight | structured load-stop location fields |
| V11 | freight | load documents table |
| V12 | accounts | preferred locale field |
| V13 | marketplace | nullable currency compatibility |
| V15 | seed data | roles, vehicle types, body types, capabilities |

V10 and V14 are intentionally unused; existing migrations were not renumbered or modified.

Total JPA entities: 40

## Package architecture (implemented)

```
uz.freight.api.<module>                  REST/WebSocket delivery and DTOs
uz.freight.<module>.application         commands, queries, and public use cases
uz.freight.<module>.domain.model        domain entities, enums, and value types
uz.freight.<module>.infrastructure      persistence adapters and repositories
uz.freight.bootstrap                    security and application configuration
uz.freight.shared                       shared error, validation, and web utilities
```

## Local development

```bash
# Infrastructure
docker compose up postgres redis -d

# Backend (local JDK 21+)
DB_URL=jdbc:postgresql://localhost:5432/freight \
DB_USERNAME=freight DB_PASSWORD=freight \
REDIS_HOST=localhost REDIS_PORT=6379 REDIS_PASSWORD="" \
REDIS_SSL_ENABLED=false JWT_PRIVATE_KEY="" \
FREIGHT_SECURITY_DEPLOYMENT=LOCAL \
./gradlew bootRun

# Or full Docker stack
docker compose up
```

Mobile connects to:
- Android emulator: `http://10.0.2.2:8080/api/v1`
- iOS simulator: `http://localhost:8080/api/v1`
- Physical device: `http://<your-local-ip>:8080/api/v1`

Dev OTP code: `123456` (any phone number)

## Tooling notes

`gh` CLI is not authenticated. Use `git push` directly (credentials cached). PRs merged via local `git merge --squash` + push to main.

## Blockers

None.
