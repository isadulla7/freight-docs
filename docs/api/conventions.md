# API konvensiyalari — API Contract v1

Ushbu hujjat `/api/v1` HTTP contractining umumiy qoidalarini belgilaydi. OpenAPI spec [freight-api-v1.yaml](../../contracts/openapi/freight-api-v1.yaml) authoritative schema source of truth; bu sahifa contractda ishlatilgan konvensiyalarni izohlaydi.

## Base path

Barcha endpointlar `/api/v1` prefiksida.

## Media type

- Request va response body: `application/json`.
- Binary upload (image/document) uchun `multipart/form-data` alohida endpointlarda.

## Authentication

[ADR-0005](../architecture/decisions/0005-authentication-and-sessions.md) bo'yicha:

- OTP orqali telefon autentifikatsiyasi.
- Muvaffaqiyatli autentifikatsiyadan keyin qisqa umrli `accessToken` va uzoq umrli `refreshToken` beriladi.
- Himoyalangan endpointlar `Authorization: Bearer <accessToken>` header talab qiladi.
- Token muddati tugaganda `/api/v1/auth/refresh` orqali yangilanadi.
- Access token ichida `UserId`, `SessionId`, `DeviceId` principal ma'lumotlari mavjud bo'ladi; bular server-side authorization uchun ishlatiladi.

## Identifikatorlar

[ADR-0011](../architecture/decisions/0011-domain-data-conventions.md) bo'yicha:

- Barcha public/domain identifikatorlar UUID v7 (yoki v4) `string` formatda.
- Sequential database ID API responseda ochilmaydi.
- OpenAPI formatda `format: uuid`.

## Vaqt

- Server timestamplari UTC'da `ISO 8601` / `date-time` formatda: `2026-08-21T14:30:00Z`.
- Client vaqtni o'z timezonesida ko'rsatadi; boshlang'ich primary timezone `Asia/Tashkent`.

## Pul (Money)

[ADR-0011](../architecture/decisions/0011-domain-data-conventions.md) bo'yicha:

```json
{
  "amount": 3000000,
  "currency": "UZS"
}
```

- `amount` — `integer` (int64), minor unit yoki whole unit (UZS uchun tiyin yo'q, shuning uchun whole unit).
- `currency` — `string`, ISO 4217 3-harfli code.
- Floating point **hech qachon** ishlatilmaydi.

## Geolokatsiya

[ADR-0014](../architecture/decisions/0014-geospatial-representation.md) bo'yicha:

```json
{
  "longitude": 69.2401,
  "latitude": 41.2995
}
```

- Coordinate tartib: `longitude` birinchi, `latitude` ikkinchi — PostGIS `geography(Point,4326)` / WGS 84 bilan mos.
- Har ikkalasi `number` (double).
- Longitude: `[-180, 180]`, Latitude: `[-90, 90]`.

## Cursor pagination

[Architecture Context §37](../../architecture/architecture-context-v1.md) bo'yicha:

Collection endpointlar cursor-based pagination ishlatadi:

**Request parametrlari:**

| Parametr | Turi | Default | Izoh |
|---|---|---|---|
| `cursor` | string | — | Oldingi responsedagi `nextCursor` qiymati |
| `limit` | integer | 20 | Sahifa hajmi, max 100 |

**Response:**

```json
{
  "items": [...],
  "nextCursor": "eyJpZCI6...",
  "hasMore": true
}
```

- `nextCursor` — keyingi sahifa uchun opaque cursor; oxirgi sahifada `null`.
- `hasMore` — qo'shimcha ma'lumot borligini ko'rsatadi.

Offset pagination ishlatilmaydi.

## Error model

Barcha xatolar bitta barqaror envelope formatda:

```json
{
  "error": {
    "code": "LOAD_NOT_FOUND",
    "message": "Yuk topilmadi",
    "details": {}
  }
}
```

| Field | Turi | Majburiy | Izoh |
|---|---|---|---|
| `code` | string | ha | Barqaror, machine-readable `UPPER_SNAKE_CASE` code |
| `message` | string | ha | Inson uchun o'qiladigan xabar; localization kelajakda |
| `details` | object | yo'q | Qo'shimcha context: validation xatolari, field nomlari va h.k. |

### Umumiy HTTP status va error code mapping

| HTTP Status | Error Code | Ma'nosi |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Request body yoki parametr noto'g'ri |
| 401 | `AUTHENTICATION_REQUIRED` | Token yo'q yoki muddati tugagan |
| 403 | `ACCESS_DENIED` | Permission yoki resource relationship yo'q |
| 404 | `{RESOURCE}_NOT_FOUND` | Resource topilmadi yoki actorga ko'rinmaydi |
| 409 | `{RESOURCE}_CONFLICT` | Concurrency yoki state conflict |
| 422 | `INVALID_STATE_TRANSITION` | State machine bo'yicha ruxsat etilmagan transition |
| 429 | `RATE_LIMIT_EXCEEDED` | So'rov chastotasi oshirildi |
| 500 | `INTERNAL_ERROR` | Server ichki xatosi |

Resurs `ACCESS_DENIED` va `NOT_FOUND` o'rtasida tanlashda, actor resursning mavjudligini bilmasligi kerak bo'lsa (authorization non-disclosure), `NOT_FOUND` qaytariladi.

## Validation xatolari

`VALIDATION_ERROR` uchun `details` field-level xatolarni qaytarishi mumkin:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "So'rov validatsiyasi muvaffaqiyatsiz",
    "details": {
      "fieldErrors": [
        {
          "field": "stops[0].location.latitude",
          "code": "OUT_OF_RANGE",
          "message": "Latitude -90 va 90 orasida bo'lishi kerak"
        }
      ]
    }
  }
}
```

## Authorization modeli

[Authorization modeli](../architecture/authorization-model.md) va [ADR-0015](../architecture/decisions/0015-authorization-and-audit.md) bo'yicha:

- Backend deny-by-default; token/UI role permission source of truth emas.
- Har endpoint uchun kerakli permission va resource relationship OpenAPI spec'da `x-required-permissions` va `x-resource-scope` bilan belgilanadi.
- Company scope'dagi operatsiyalarda `X-Company-Id` headeri yoki path parametri orqali company context uzatiladi.
- Resurs topilmasligi yoki actorga ko'rinmasligi bir xil `404` bilan javob beradi (non-disclosure).

## Idempotency

High-risk mutation endpointlar `Idempotency-Key` headerni qo'llab-quvvatlaydi. Kelajakda financial/payment endpointlar uchun majburiy bo'ladi.

## Client generation compatibility

OpenAPI spec [ADR-0007](../architecture/decisions/0007-openapi-contract.md) bo'yicha Dart (openapi-generator) va TypeScript (openapi-typescript) client generatsiyasi uchun mos bo'lishi kerak:

- Barcha schemalar nomlangan va `$ref` orqali ishlatiladi.
- `oneOf`/`anyOf` minimal; discriminator aniq.
- Enum qiymatlari barqaror va versioned.
- Nullable fieldlar `nullable: true` bilan belgilanadi (OAS 3.0 mos kelish uchun); OAS 3.1 da JSON Schema null type ham qo'llanishi mumkin.
