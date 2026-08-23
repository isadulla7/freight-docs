# Next task

Phases 5–10 (Identity, Accounts, Fleet, Freight, Marketplace, Shipment) are COMPLETE and merged.

- **Current phase:** Communication (phase 11)
- **Last completed phase:** Shipment (phase 10) — `bb9c2d4`
- **Next task:** Communication module — Conversations, messages, notifications
- **Architecture spec:** [docs/architecture/module-boundaries.md](docs/architecture/module-boundaries.md)
- **Database spec:** [docs/architecture/database-erd.md](docs/architecture/database-erd.md)
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before any implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `docs/architecture/module-boundaries.md` (communication section)
5. Read `docs/architecture/database-erd.md` (communication tables)
6. Inspect the current `freight-backend` GitHub `main`
7. Confirm no open PRs conflict

Do not rely on previous chat history.

## What is done

**Identity (phase 5):** All public API members merged including `VerifyOtpAndRegister`.

**Accounts (phase 6):** All 12 services implemented and merged.

**Fleet (phase 7):** V4 migration (8 tables), vehicle lifecycle, geospatial search.

**Freight (phase 8):** V5 migration (5 tables), load lifecycle with optimistic locking, MatchLoad concurrency-safe.

**Marketplace (phase 9):** V6 migration (1 table — offers), CreateOffer, WithdrawOffer, RejectOffer, AcceptOffer (concurrency-safe, auto-rejects remaining pending), GetOfferSummary, ListOffersForLoad.

**Shipment (phase 10):** V7 migration (2 tables — shipments, shipment_status_history), CreateShipment (idempotent by offer_id), GetShipment, ListUserShipments, UpdateShipmentStatus (state machine: CREATED→IN_TRANSIT→DELIVERED→COMPLETED, cancellable), GetShipmentStatusHistory.

## Phase 11: Communication

Implement the communication module — conversations, messaging, notifications:

### Database (V8 migration)

Create `communication` schema tables per `database-erd.md`:
- `conversations` — shipment-scoped, type/status
- `conversation_participants` — composite PK, role, read tracking
- `messages` — immutable, sender, body, sequence
- `notifications` — recipient, type, source, read state
- `communication_preferences` — user channel/type preferences
- `push_endpoints` — device push tokens
- `notification_deliveries` — delivery attempts, idempotency

### Module dependencies

`communication` depends on: `accounts`, `fleet`, `freight`, `identity`, `marketplace`, `shipment` (per `module-boundaries.md`).

### Public API services

1. **GetOrCreateShipmentConversation** — find or create conversation for a shipment
2. **SendMessage** — add message to conversation
3. **ListMessages** — paginated message list
4. **MarkConversationRead** — update read position
5. **ListNotifications** — user notifications
6. **MarkNotificationRead** — mark notification as read
7. **UpdateCommunicationPreferences** — user preference management
8. **RegisterPushEndpoint** — device push token registration

### Testing

- Integration tests (Testcontainers) for all services
- Public API surface test
- Update PersistenceFoundationTests and PostgreSqlIntegrationTests

## Blockers

None.
