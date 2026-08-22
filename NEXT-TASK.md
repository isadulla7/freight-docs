# Next task

Phases 5–9 (Identity, Accounts, Fleet, Freight, Marketplace) are COMPLETE and merged.

- **Current phase:** Shipment (phase 10)
- **Last completed phase:** Marketplace (phase 9) — `a146a97`
- **Next task:** Shipment module — Shipment lifecycle from accepted offer
- **Architecture spec:** [docs/architecture/module-boundaries.md](docs/architecture/module-boundaries.md)
- **Database spec:** [docs/architecture/database-erd.md](docs/architecture/database-erd.md)
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before any implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `docs/architecture/module-boundaries.md` (shipment section)
5. Read `docs/architecture/database-erd.md` (shipment tables)
6. Inspect the current `freight-backend` GitHub `main`
7. Confirm no open PRs conflict

Do not rely on previous chat history.

## What is done

**Identity (phase 5):** All public API members merged including `VerifyOtpAndRegister`.

**Accounts (phase 6):** All 12 services implemented and merged. Full authorization model (roles, permissions, company member roles).

**Fleet (phase 7):** V4 migration (8 tables), PostGIS geospatial search, vehicle lifecycle. Services: CreateVehicle, UpdateVehicle, DeactivateVehicle, GetVehicleSummary, VerifyVehicle, PublishAvailableVehicle, CloseAvailableVehicle, SearchNearbyVehicles, ValidateVehicleEligibility.

**Freight (phase 8):** V5 migration (5 tables), load lifecycle with optimistic locking. Services: CreateLoad, UpdateDraftLoad, PublishLoad, CancelLoad, ExpireLoad, MatchLoad (concurrency-safe), GetLoadSummary, SearchLoads, ValidateOfferEligibility.

**Marketplace (phase 9):** V6 migration (1 table — offers with partial unique, XOR offerer, optimistic locking). Services: CreateOffer, WithdrawOffer, RejectOffer, AcceptOffer (concurrency-safe via freight.MatchLoad, auto-rejects remaining pending), GetOfferSummary, ListOffersForLoad. Cross-module: freight.ValidateOfferEligibility, fleet.ValidateVehicleEligibility.

## Phase 10: Shipment

Implement the shipment module — transport execution from accepted offers:

### Database (V7 migration)

Create `shipment` schema tables per `database-erd.md`:
- `shipments` — with @Version for optimistic locking, status enum, assignment snapshot
- `shipment_stops` — immutable execution snapshot of load stops
- `shipment_status_history` — append-only status change log

### Module dependencies

`shipment` depends on: `accounts`, `fleet`, `freight`, `marketplace` (per `module-boundaries.md`).

### Public API services

1. **GetShipment** — read model
2. **ListUserShipments** — query by user
3. **UpdateShipmentStatus** — state machine transitions
4. **GetShipmentStatusHistory** — append-only history read

### Event consumption

- `OfferAccepted` — create shipment reliably and idempotently from the accepted offer

### Testing

- Integration tests (Testcontainers) for all services
- Public API surface test
- Update PersistenceFoundationTests (entity count) and PostgreSqlIntegrationTests (Flyway version "7", shipment table assertions)

## Blockers

None. All prerequisites for Phase 10 (Shipment) are merged.
