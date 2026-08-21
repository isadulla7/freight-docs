# Boshlang‘ich domen modeli — Architecture v1.0 Lock

Model DDD-lite: faqat mustaqil invariant, lifecycle yoki concurrency boundary bo‘lgan tushuncha aggregate root bo‘ladi. Typed IDlar UUID, pul `Money`, vaqt UTC qoidalari [ADR-0011](decisions/0011-domain-data-conventions.md)ga amal qiladi.

## Identity

### `AuthenticationIdentity` aggregate root

- **Owns:** normalized phone identifier, verification/status va linked `UserId`.
- **Invariants:** bitta normalized telefon uchun ko‘pi bilan bitta active identity; phone OTP bilan tasdiqlanmasdan authenticated session berilmaydi; identity business profile emas.
- **Creation:** successful OTP verification vaqtida `accounts.ProvisionUser` bilan sync user yaratiladi/bog‘lanadi.
- **Mutation:** phone change/account recovery alohida high-risk flow; v1 policy [open question](open-questions.md).
- **ID references:** `UserId` only.
- **Must not contain:** name/profile, role, company, driver/vehicle data.

### `AuthSession` aggregate root

- **Owns:** hashed refresh credential, `AuthDeviceId`, issued/expiry/revoked timestamps, rotation/reuse-detection metadata.
- **Invariants:** raw token saqlanmaydi; expired/revoked/reused session refresh qilinmaydi; har session bitta user va device scope’da.
- **Creation/mutation:** successful authentication yaratadi; refresh rotation qiladi; user/security action revoke qiladi.
- **Must not contain:** permissions snapshotini uzoq muddatli source of truth sifatida.

`AuthDevice` user qurilmasining security-facing metadata entitysi. Push token `communication.PushEndpoint`ga tegishli; OTP challenge esa TTL’li transient value bo‘lib aggregate/table emas.

## Accounts

### `User` aggregate root

- **Owns:** display name, account status va minimal locale/timezone preference.
- **Invariants:** stable UUID; authentication phone credentialsini saqlamaydi; status o‘zgarishi authorization bilan.
- **Creation:** faqat identity registration public API orqali idempotent provisioning.
- **Mutation:** user yoki scoped support permission; blocking session revocation reactionini talab qiladi.
- **References:** external auth identity UserId orqali ayni userni ko‘rsatadi.
- **Must not contain:** vehicle, load, offer, shipment collections yoki UI `userType` flag.

### `DriverProfile` aggregate root

- **Identity:** `DriverProfileId`, unique `UserId`.
- **Owns:** driver-specific profile fields, verification status, `DriverDocumentMetadata`, verification decisions.
- **Invariants:** bir userga ko‘pi bilan bitta driver profile; verified holat faqat `DRIVER_VERIFY`; document private storage key orqali, public URL bilan emas.
- **Creation:** authenticated user minimal profiledan explicit driver onboarding boshlaganda.
- **Mutation:** owner oddiy profilni tahrirlaydi; verification status/reasonni faqat scoped verifier; document read alohida `DRIVER_DOCUMENT_READ`.
- **Must not contain:** vehicle verification, offer yoki shipment history collection.

### `Company` aggregate root

- **Owns:** company business profile va membership lifecycle; `CompanyMember` membership entitysi.
- **Invariants:** company ko‘p memberga ega bo‘lishi mumkin; active membership `(companyId,userId)` unique; membershipdagi role faqat company-scoped; oxirgi ownerni olib tashlash qoidasi MVP role seed policy bilan birga test qilinadi.
- **Creation:** authorized user company yaratib initial owner member bo‘ladi.
- **Mutation:** `COMPANY_MEMBER_ADD/REMOVE` va company resource scope tekshiruvi bilan.
- **References:** `UserId` only.
- **Must not contain:** user auth session, memberning vehicle/load aggregate’lari.

### `Role` va `Permission`

Reference/config model: `Permission` barqaror capability code; `Role` named permission set. Global `UserRole` user-level capability, `CompanyMemberRole` membership scope’dagi capability beradi. Ular UI label emas va business resource ownership checkini almashtirmaydi.

## Fleet

### `Vehicle` aggregate root

- **Owns:** brand/model/year/plate, capacity, volume, active state, `VehicleCapability` assignment, vehicle document metadata va verification history.
- **Value/reference:** `VehicleTypeId`, `BodyTypeId`, `VehicleCapabilityId`, `Weight`, `Volume`, `PlateNumber`, `VerificationStatus`.
- **Invariants:** owner `UserId` yoki manager `CompanyId`dan aniq biri; plate normalized uniqueness policy; capacity/volume manfiy emas; verified qarorni faqat scoped verifier; inactive vehicle yangi offer/availability uchun eligible emas.
- **Creation:** `VEHICLE_CREATE` + owner/company scope; type/body backend reference data’dan.
- **Mutation:** owner/company `VEHICLE_EDIT`; verification evidencega ta’sir qiluvchi field o‘zgarishi verification policy bo‘yicha re-review talab qiladi.
- **References:** accounts IDs only.
- **Must not contain:** current availability location, offer, load yoki shipment status.

### `AvailableVehicle` aggregate root

- **Owns:** `VehicleId`, current `GeoPoint`, optional desired destination/direction point, availability window va listing status.
- **Invariants:** `Vehicle != AvailableVehicle`; active/eligible vehiclegina active availability beradi; window start < end; exact location server-side scoped response bilan; closed/expired listing qayta mutatsiya qilinmaydi.
- **Creation/mutation:** vehicle boshqaruvchisi publish/close qiladi; expiration system action.
- **Must not contain:** vehicle master data nusxasi yoki live GPS history.

`VehicleType`, `BodyType`, `VehicleCapability` backend-driven reference entitylar; ular uchun alohida aggregate ceremony talab qilinmaydi.

## Freight

### `Load` aggregate root

- **Owns:** `Cargo`, ordered `LoadStop`, `LoadRequirement`, `LoadPricing`, `CargoImageMetadata`, lifecycle va optimistic `version`.
- **Value objects:** `Money`, `GeoPoint`, `Weight`, `Volume`, `StopSequence`, `PricingMode` (`FIXED_PRICE` yoki `REQUEST_OFFERS`).
- **Invariants:** kamida bitta `PICKUP` va bitta `DELIVERY`; sequence unique va yo‘l tartibini beradi; publish vaqtida valid cargo, stops, requirement va pricing intent; fixed price uchun amount/currency majburiy; pul float emas; `MATCHED` faqat bitta winning `OfferId`; terminal load tahrirlanmaydi.
- **Creation:** `LOAD_CREATE`; individual owner `UserId` yoki `CompanyId`dan bittasi; initial `DRAFT`.
- **Mutation:** draft owner/scoped company member tahrirlaydi; publish/cancel/expire/match [state machine](state-machines.md) bo‘yicha.
- **References:** owner accounts IDlari, fleet type/body/capability IDlari, accepted `OfferId`; ID only.
- **Must not contain:** offers collection, assigned driver execution, shipment status/history, notification yoki payment.

`Cargo`, `LoadRequirement`, `LoadPricing` domain value/object entitylari bo‘lishi mumkin, lekin mustaqil repository yoki lifecycle yo‘q. Image binary object storage’da; aggregate faqat private storage key, media type, size/hash va order metadata saqlaydi.

## Marketplace

### `Offer` aggregate root

- **Owns:** `LoadId`, carrier `UserId` yoki `CompanyId`, `DriverProfileId`, `VehicleId`, offered `Money`, optional message, status va version.
- **Invariants:** faqat eligible published loadga; creator eligible driver/carrier va vehicle’ni boshqaradi; amount musbat va load currency bilan mos; terminal offer o‘zgarmaydi; load uchun faqat bitta accepted offer.
- **Creation:** `OFFER_CREATE` + server-side load/driver/vehicle eligibility.
- **Mutation:** creator pending offerni withdraw qiladi; load owner/scoped member pending offerni accept/reject qiladi; expiry system action; acceptance load lock/version va database unique constraint bilan concurrency-safe.
- **References:** other-module IDs only.
- **Must not contain:** full load/cargo/vehicle/profile snapshot master data, shipment lifecycle yoki payment.

## Shipment

### `Shipment` aggregate root

- **Owns:** accepted `OfferId`, `LoadId`, shipper User/Company ID, `ShipmentAssignment` (driver/carrier/vehicle IDs), current status, version va execution timestamps.
- **Invariants:** faqat accepted offerdan idempotent yaratiladi; one shipment per accepted offer/load; status faqat [shipment state machine](state-machines.md) bo‘yicha; actor assignment/ownership va permission bilan mos; har transition immutable history bilan bir transactionda.
- **Creation:** reliable `OfferAccepted` handler. `CREATED -> DRIVER_ASSIGNED` system assignment accepted offer ma’lumotidan.
- **Mutation:** assigned driver execution statuslarini, shipper `DELIVERED -> COMPLETED`ni bajaradi; admin faqat explicit scoped permission va audited exceptional operation bilan, lekin v1 normal state bypass yo‘q.
- **References:** other-module IDs only; zarur execution snapshot (stop/address/contact va accepted price) tarixiy barqarorlik uchun creation vaqtida nusxalanishi mumkin.
- **Must not contain:** mutable source `Load`, `Offer`, `Vehicle` entitylari; chat messages; dispute/payment state.

`ShipmentStatusHistory` aggregate child emas, aggregate transitioni bilan yoziladigan append-only record. Tashqaridan update/delete API yo‘q.

## Communication

### `Conversation` aggregate root

- **Owns:** shipment scope reference, status va `ConversationParticipant` membership.
- **Invariants:** participant bo‘lmagan actor metadata yoki message historyni ko‘rmaydi; MVP shipment uchun ko‘pi bilan bitta active conversation; participant set source business relationshipdan yaratiladi.
- **Creation:** idempotent `ShipmentCreated` reactioni.
- **Mutation:** participant/read state va close; participantni self-service qo‘shish yo‘q.
- **Must not contain:** unbounded messages collection, phone number exposure policy yoki shipment state.

### `Message` aggregate root

- **Owns:** `ConversationId`, sender `UserId`, content type/body yoki attachment metadata, created timestamp.
- **Invariants:** sender active participant; message append-only; content/payload limits; attachment private/scoped; edit/delete policy v1 da yo‘q.
- **Creation:** `SendMessage` authorizationdan keyin.
- **Must not contain:** delivery provider response yoki boshqa modul entitysi.

### `Notification` aggregate root

- **Owns:** recipient, type/template data, source resource IDs, read state va delivery intent.
- **Invariants:** recipientga zarur bo‘lmagan sensitive payload kiritilmaydi; provider retry bir notification/delivery key bo‘yicha idempotent.

`CommunicationPreference` user/channel/type bo‘yicha opt-in/out value; transactional/security xabarlarining majburiyligi product/legal qaroriga bog‘liq. `PushEndpoint` delivery tokenini encrypted/protected ko‘rinishda device bilan bog‘laydi, lekin auth session emas.

## Aggregate kesish qoidasi

- cross-aggregate consistency imkon qadar ID + application orchestration orqali;
- cross-module transaction faqat `AcceptOffer -> MatchLoad + OfferAccepted publication` kabi kuchli invariant talab qilgan joyda;
- katta/unbounded collection (`Message`, history) root ichida load qilinmaydi;
- read model aggregate emas va jOOQ query domain write modelini chetlab o‘tib mutation qilmaydi.
