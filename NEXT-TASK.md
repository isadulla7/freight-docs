# Next task

Phases 5–8 (Identity, Accounts, Fleet, Freight) are COMPLETE and merged.

- **Current phase:** Marketplace (phase 9)
- **Last completed phase:** Freight (phase 8) — `cffb69f`
- **Next task:** Marketplace module — Offer lifecycle with concurrency-safe winner selection
- **Architecture spec:** [docs/architecture/module-boundaries.md](docs/architecture/module-boundaries.md)
- **Database spec:** [docs/architecture/database-erd.md](docs/architecture/database-erd.md)
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before any implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `docs/architecture/module-boundaries.md` (marketplace section)
5. Read `docs/architecture/database-erd.md` (marketplace tables)
6. Inspect the current `freight-backend` GitHub `main`
7. Confirm no open PRs conflict

Do not rely on previous chat history.

## What is done

**Identity (phase 5):** All public API members merged including `VerifyOtpAndRegister`.

**Accounts (phase 6):** All 12 services implemented and merged. Full authorization model (roles, permissions, company member roles).

**Fleet (phase 7):** V4 migration (8 tables), PostGIS geospatial search, vehicle lifecycle. Services: CreateVehicle, PublishAvailableVehicle, SearchNearbyVehicles, ValidateVehicleEligibility.

**Freight (phase 8):** V5 migration (5 tables), load lifecycle with optimistic locking. Services: CreateLoad, UpdateDraftLoad, PublishLoad, CancelLoad, ExpireLoad, MatchLoad (concurrency-safe), GetLoadSummary, SearchLoads, ValidateOfferEligibility.

## Phase 9: Marketplace

Implement the marketplace module — the offer/bidding layer between drivers/carriers and shippers:

### Database (V6 migration)

Create `marketplace` schema tables per `database-erd.md`:
- `offers` — with @Version for optimistic locking, status enum (PENDING/ACCEPTED/REJECTED/WITHDRAWN/EXPIRED/COUNTERED)
- `offer_counter_offers` — counter-offer chain

### Module dependencies

`marketplace` depends on: `accounts`, `fleet`, `freight` (per `module-boundaries.md`).

### Public API services

1. **CreateOffer** — validate load eligibility (`freight.ValidateOfferEligibility`), validate vehicle eligibility (`fleet.ValidateVehicleEligibility`), create PENDING offer
2. **WithdrawOffer** — PENDING → WITHDRAWN
3. **AcceptOffer** — PENDING → ACCEPTED, then call `freight.MatchLoad` (concurrency-safe: if MatchLoad returns VersionConflict, reject this acceptance)
4. **RejectOffer** — PENDING → REJECTED
5. **CounterOffer** — create counter-offer record, mark offer as COUNTERED
6. **GetOfferSummary** — read model
7. **ListOffersForLoad** — query by loadId

### Testing

- Integration tests (Testcontainers) for all services
- Public API surface test
- Update PersistenceFoundationTests (entity count) and PostgreSqlIntegrationTests (Flyway version "6", marketplace table assertions)

### Key design decisions

- **Concurrency-safe winner selection**: AcceptOffer calls `freight.MatchLoad` with the load's current version. If another offer was accepted first (VersionConflict), AcceptOffer must fail gracefully.
- **XOR owner on offer**: either `offerer_user_id` or `offerer_company_id`, not both.
- **Cross-module calls are by UUID** (no FK across schemas).

## Blockers

None. `freight.ValidateOfferEligibility` and `freight.MatchLoad` are the marketplace's prerequisites and are already merged.
