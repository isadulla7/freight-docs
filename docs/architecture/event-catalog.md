# Domain/application event katalogi — Architecture v1.0 Lock

Cross-module reaction uchun [ADR-0008](decisions/0008-internal-events.md) qo‘llanadi: Spring application eventlari, zarur joyda Spring Modulith reliable publication; Kafka/RabbitMQ yo‘q. Eventlar business command o‘rnini bosmaydi va har CRUD action event bo‘lmaydi.

## Contract qoidalari

Har e’lon qilingan event envelope’i kamida `eventId` (UUID), `eventName`, `schemaVersion`, `occurredAt` (UTC), `aggregateId`, `correlationId` va kerak bo‘lsa `actorUserId`ni saqlaydi. Payload minimal va versioned; sensitive document, raw contact, token, OTP yoki chat body cross-module eventga qo‘yilmaydi.

- `sync` — public application API va caller transactionining bir qismi; event emas.
- `reliable async` — source commitdan keyin publication registry orqali retry qilinadi.
- consumer `(eventId, consumer)` bo‘yicha duplicate processingga idempotent;
- ordering faqat ayni aggregate uchun version/status orqali tekshiriladi; global ordering taxmin qilinmaydi;
- permanent failure operational alert/dead publication visibility talab qiladi, lekin yangi broker talab qilmaydi.

## Event katalogi

| Event | Publisher | Trigger | Minimum payload | Consumer | Expectation / idempotency |
| --- | --- | --- | --- | --- | --- |
| `UserRegistered` | `identity` | OTP verification va sync `accounts.ProvisionUser` muvaffaqiyatli | `eventId`, `userId`, `identityId`, `occurredAt` | `communication` welcome/security notification | reliable async; recipient+event dedupe |
| `CompanyMemberAdded` | `accounts` | membership active bo‘ldi | `eventId`, `companyId`, `memberId`, `userId`, `roleIds`, `actorUserId` | `communication` | reliable async; membership ID dedupe |
| `CompanyMemberRemoved` | `accounts` | membership inactive bo‘ldi | `eventId`, `companyId`, `memberId`, `userId`, `actorUserId` | affected modules cache/in-flight access invalidation only if needed; `communication` | reliable async; authorizationning source of truthi har requestda accounts, event cache correctnessga yagona tayanch emas |
| `DriverVerificationChanged` | `accounts` | scoped verifier statusni o‘zgartirdi | `eventId`, `driverProfileId`, `userId`, `previousStatus`, `newStatus`, `decisionId` | `fleet`, `communication` | reliable async; latest version guard |
| `VehicleVerified` | `fleet` | vehicle verification `VERIFIED`ga o‘tdi | `eventId`, `vehicleId`, `ownerUserId`/`companyId`, `verificationId` | `marketplace`, `communication` | reliable async; vehicle/version dedupe |
| `AvailableVehiclePublished` | `fleet` | availability active qilindi | `eventId`, `availableVehicleId`, `vehicleId`, coarse/search-safe location cell yoki location reference, `availableFrom`, `availableUntil` | `marketplace` matching reaction if enabled | reliable async; exact location payloadini notificationga uzatmaslik |
| `LoadPublished` | `freight` | load `DRAFT -> PUBLISHED` | `eventId`, `loadId`, owner IDs, pickup/delivery location reference yoki coarse search fields, requirement IDs, pickup window | `marketplace`, `communication` only for configured matching/notification | reliable async; load version guard |
| `LoadMatched` | `freight` | concurrency-safe `PUBLISHED -> MATCHED` | `eventId`, `loadId`, `acceptedOfferId`, `actorUserId` | audit/observability reaction only; shipment `OfferAccepted`ni consume qiladi | same transaction publication; duplicate-safe |
| `LoadCancelled` | `freight` | cancellable load cancelled | `eventId`, `loadId`, `actorUserId`, optional reasonCode | `marketplace`, `communication` | reliable async; marketplace faqat pending offerlarni idempotent reject qiladi |
| `LoadExpired` | `freight` | expiry policy | `eventId`, `loadId`, `expiredAt` | `marketplace` | reliable async; pending-only update |
| `OfferCreated` | `marketplace` | valid offer persisted | `eventId`, `offerId`, `loadId`, creator/carrier IDs, `vehicleId`, `amount`, `currency` | `communication` | reliable async; notification dedupe; message may be omitted/minimized |
| `OfferAccepted` | `marketplace` | load match + winner accept transaction committed | `eventId`, `offerId`, `loadId`, shipper IDs, carrier/driver IDs, `vehicleId`, accepted `amount/currency` | `shipment`, `communication` | **reliable async**; shipment unique `offerId/loadId` makes creation idempotent; retry until created or operator-visible failure |
| `OfferRejected` | `marketplace` | explicit rejection yoki competing winner/load close | `eventId`, `offerId`, `loadId`, creator IDs, `reasonCode` | `communication` where user-visible | reliable async; bulk rejection notification policy may suppress duplicates, business status may not |
| `OfferWithdrawn` | `marketplace` | creator withdraws pending offer | `eventId`, `offerId`, `loadId`, `actorUserId` | communication only if product needs shipper notification | ordinary async; no cross-module consumer required by MVP |
| `OfferExpired` | `marketplace` | expiry policy | `eventId`, `offerId`, `loadId`, `expiredAt` | no mandatory MVP consumer | event optional unless a reaction is configured |
| `ShipmentCreated` | `shipment` | accepted offerdan idempotent shipment yaratildi | `eventId`, `shipmentId`, `offerId`, `loadId`, participant user/company IDs | `communication` conversation/notification | reliable async; unique shipment conversation scope |
| `ShipmentStatusChanged` | `shipment` | valid lifecycle transition | `eventId`, `shipmentId`, `previousStatus`, `newStatus`, `actorUserId`/`SYSTEM`, `changedAt`, optional reasonCode | `communication`; audit sink only if required | reliable async; `(shipmentId,newVersion)` dedupe |
| `ShipmentDelivered` | `shipment` | first transition to `DELIVERED` | `eventId`, `shipmentId`, shipper IDs, carrier/driver IDs, `deliveredAt` | `communication` confirmation prompt | reliable async; once per shipment |
| `ShipmentCompleted` | `shipment` | first transition to `COMPLETED` | `eventId`, `shipmentId`, participant IDs, `completedAt` | `communication`; future ratings module only after ADR | reliable async; once per shipment |
| `MessageCreated` | `communication` | authorized message append | `eventId`, `messageId`, `conversationId`, `senderUserId`, recipient IDs, `createdAt` | `communication` realtime/push delivery handler | internal application event, async delivery; body fetched only after participant authorization, not copied to provider event/log |

## Sync coordination, event emas

- `identity -> accounts.ProvisionUser`: token berishdan oldin stable User mavjud bo‘lishi kerak.
- `marketplace.AcceptOffer -> freight.MatchLoad`: bitta winner invariantining transaction boundarysi.
- application use case -> `accounts.Authorize`: security qarori request ichida fail-closed.
- `CreateOffer` eligibility uchun accounts/fleet/freight public querylari: stale event projection yagona security/business gate emas.

`OfferAccepted -> ShipmentCreated` reliable async tanlandi: modul couplingni kamaytiradi, publication registry retry beradi. Accepted offer shipmentga aylanmay qolgan holat monitoring/operational reconciliationda ko‘rinishi shart; bu “fire-and-forget” emas.
