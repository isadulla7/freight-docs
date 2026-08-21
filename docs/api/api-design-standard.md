# API dizayn standarti — API Contract v1

## Resource naming

- Resource nomlari inglizcha, plural: `/loads`, `/offers`, `/shipments`, `/vehicles`.
- Nested resource ota-bola munosabatini ifodalaydi: `/loads/{loadId}/offers`, `/shipments/{shipmentId}/conversation`.
- Action endpoint foydalanuvchi (REST resource collection CRUD bilan ifodalanmaydigan domain command) uchun verb: `/loads/{loadId}/publish`, `/offers/{offerId}/accept`.
- `camelCase` JSON field nomlari: `vehicleTypeId`, `createdAt`, `pickupAddress`.
- Path parametrlar `camelCase`: `{loadId}`, `{companyId}`.
- Query parametrlar `camelCase`: `pickupLongitude`, `vehicleTypeId`, `minWeightKg`.

## HTTP method konvensiyalari

| Method | Semantika | Idempotent |
|---|---|---|
| `GET` | Resource o'qish, search, listing | Ha |
| `POST` | Resource yaratish yoki domain action | Yo'q (Idempotency-Key bilan ha) |
| `PATCH` | Qisman yangilash | Yo'q |
| `PUT` | To'liq almashtirish yoki upsert | Ha |
| `DELETE` | Resource o'chirish | Ha |

## HTTP status code konvensiyalari

| Status | Qo'llanish |
|---|---|
| `200` | Muvaffaqiyatli o'qish yoki yangilash |
| `201` | Yangi resource yaratildi |
| `204` | Muvaffaqiyatli, lekin response body yo'q |
| `400` | Validation xatosi |
| `401` | Authentication talab qilinadi |
| `403` | Authorization rad etildi |
| `404` | Resource topilmadi (yoki non-disclosure) |
| `409` | Conflict (concurrency yoki business state) |
| `422` | Business rule violation (invalid state transition) |
| `429` | Rate limit oshirildi |
| `500` | Server ichki xatosi |
| `503` | Service temporarily unavailable |

## Request/response naming

- Request schemaları: `Create{Resource}Request`, `Update{Resource}Request`, `{Action}Request`.
- Response schemalar resource nomi: `Load`, `Offer`, `Shipment`, yoki collection uchun `{Resource}Page`.
- Summary/compact response: `{Resource}Summary` — feed va listlar uchun.
- Input schemalar: `{Resource}Input` — nested value objectlar uchun (masalan `CargoInput`, `LoadStopInput`).

## Enum va reference data

Kategoriyalar:

| Kategoriya | Xatti-harakatlar | O'zgartirish |
|---|---|---|
| **Domain enum/state** | `LoadStatus`, `OfferStatus`, `ShipmentStatus`, `StopType`, `PricingMode` | Faqat ADR bilan; schema va state machine o'zgarishi |
| **Backend reference data** | `VehicleType`, `BodyType`, `VehicleCapability` | Backend-driven; client API orqali oladi; server tomonidan boshqariladi |
| **Feature/config metadata** | `FeatureCapabilities`, `SupportedCurrency` | Server konfiguratsiyasi; runtime o'zgartirish |

Domain enumlar OpenAPI `enum` bilan; reference data alohida endpoint orqali olindi. Client noma'lum enum qiymatiga crash qilmasligi kerak — `default`/`unknown` handlerga ega bo'lishi kerak.

## Timestamp representation

- Barcha timestamplar UTC, `ISO 8601` `date-time` format: `2026-08-21T14:30:00Z`.
- Sana (time bo'lmasa): `date` format: `2028-12-31`.
- Client vaqtni foydalanuvchi timezoneida ko'rsatadi.

## UUID representation

- Barcha public/domain IDlar `string` `format: uuid`.
- Sequential database ID API da ochilmaydi.

## Money representation

Batafsil: [conventions.md](conventions.md#pul-money).

## Geolocation representation

Batafsil: [conventions.md](conventions.md#geolokatsiya).

API GeoPoint DTO: `{ longitude, latitude }`. Provider-specific response turlari API da ochilmaydi.

Kelajakda route distance/duration qo'shilishi mumkin (masalan `estimatedDistanceKm`, `estimatedDurationMinutes` response fieldlari).

## Pagination

Batafsil: [pagination-filtering.md](pagination-filtering.md).

## Filtering va sorting

Batafsil: [pagination-filtering.md](pagination-filtering.md).

## Idempotency

High-risk mutation endpointlar `Idempotency-Key` header bilan idempotent bo'lishi kerak:

- Offer acceptance (`POST /offers/{offerId}/accept`)
- Kelajak payment operatsiyalari
- Tanlangan shipment transitionlari (server konfiguratsiyasi)

Semantika:
- Birinchi request normal bajariladi va natijani `Idempotency-Key` bilan cache qiladi.
- Takroriy request (bir xil key) cached natijani qaytaradi, qayta bajarmaydi.
- Boshqa request body bilan bir xil key yuborilsa `409 IDEMPOTENCY_KEY_CONFLICT`.
- Key scope: foydalanuvchi va endpoint bo'yicha.
- Key retention: server konfiguratsiyasi (masalan 24 soat).

## Optimistic concurrency

`Load`, `Offer`, `Shipment`, `Vehicle`, `AvailableVehicle` aggregatlari `version` (int64) fieldiga ega. Mutation requestlarda `expectedVersion` yuboriladi; version mismatch `409 CONCURRENCY_CONFLICT` qaytaradi.

## Tracing va request ID

- Server har response uchun `X-Request-Id` header generatsiya qiladi.
- Error response body `traceId` fieldini qaytaradi.
- Client qo'shimcha context uchun `X-Request-Id` header yuborishi mumkin.

## Localization

- Error `message` kelajakda localize qilinishi mumkin.
- Client `Accept-Language` header yuborishi mumkin; v1 da server default (uz/ru/en TBD).
- Reference data `label` kelajakda localized response bilan qaytarilishi mumkin.

## Rate limiting

- Rate-limited response: `429` + `Retry-After` header (soniyalarda).
- Error code: `RATE_LIMIT_EXCEEDED`.
- Response headerlari: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`.
- Exact limitlar server konfiguratsiyasi; contract faqat formatni belgilaydi.

## API versioning va deprecation

- Major version URL da: `/api/v1`, `/api/v2`.
- Minor/additive o'zgarishlar versiya oshirmasdan qo'shiladi (backward compatible).
- Deprecated field/endpoint `Deprecated` OpenAPI annotation va `Sunset` header bilan belgilanadi.
- Minimal deprecation window: 2 major mobile release yoki 90 kun (qaysi uzunroq bo'lsa).
- Mobile clientlar backend versiyasidan orqada qolishi mumkin; server yangi optional fieldlarni qo'shadi, ammo mavjud fieldlarni o'zgartirmaydi.

Batafsil: [compatibility.md](compatibility.md).

## Generic endpoint taqiqi

Quyidagi anti-pattern taqiqlanadi:

```
POST /api/action
{ "entity": "load", "action": "create", "data": {...} }
```

Har bir business operatsiya o'z typed endpointiga ega. Dinamik xatti-harakatlar flexible typed filterlar, backend-driven metadata, konfiguratsiya va additive fieldlar orqali ta'minlanadi.
