# Error model — API Contract v1

## Umumiy error envelope

Barcha xatolar bitta barqaror formatda:

```json
{
  "error": {
    "code": "LOAD_ALREADY_MATCHED",
    "message": "Load already has an accepted offer",
    "traceId": "req_abc123def456",
    "details": {}
  }
}
```

| Field | Turi | Majburiy | Izoh |
|---|---|---|---|
| `code` | string | Ha | Barqaror machine-readable code (`UPPER_SNAKE_CASE`) |
| `message` | string | Ha | Inson uchun xabar; kelajakda localize qilinishi mumkin |
| `traceId` | string | Ha | Request trace ID; debugging va support uchun |
| `details` | object | Yo'q | Qo'shimcha context |

**Qoidalar:**
- Client `code` ga asoslanadi, `message` textini parse qilmaydi.
- Stack trace, internal exception yoki ichki path hech qachon ochilmaydi.
- Security-sensitive xatolar resource mavjudligini oshkor qilmaydi.

## Error code katalogi

### Authentication xatolari

| HTTP | Code | Ma'nosi |
|---|---|---|
| 401 | `AUTHENTICATION_REQUIRED` | Token yo'q, muddati tugagan yoki noto'g'ri |
| 401 | `SESSION_REVOKED` | Session revoke qilingan |
| 401 | `TOKEN_EXPIRED` | Access token muddati tugagan; refresh talab qilinadi |

### Authorization xatolari

| HTTP | Code | Ma'nosi |
|---|---|---|
| 403 | `ACCESS_DENIED` | Permission yoki resource relationship yo'q |
| 404 | `{RESOURCE}_NOT_FOUND` | Resource topilmadi yoki actorga ko'rinmaydi (non-disclosure) |

### Validation xatolari

| HTTP | Code | Ma'nosi |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Request body yoki parametr noto'g'ri |
| 400 | `INVALID_FORMAT` | Ma'lumot formati noto'g'ri (masalan UUID, phone) |

Validation error `details`:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "So'rov validatsiyasi muvaffaqiyatsiz",
    "traceId": "req_xyz789",
    "details": {
      "fieldErrors": [
        {
          "field": "stops[0].location.latitude",
          "code": "OUT_OF_RANGE",
          "message": "Latitude -90 va 90 orasida bo'lishi kerak"
        },
        {
          "field": "cargo.weightKg",
          "code": "MIN_VALUE",
          "message": "Og'irlik 1 dan kam bo'lishi mumkin emas"
        }
      ]
    }
  }
}
```

Field error codlari: `REQUIRED`, `MIN_VALUE`, `MAX_VALUE`, `OUT_OF_RANGE`, `INVALID_FORMAT`, `TOO_LONG`, `TOO_SHORT`, `INVALID_ENUM`.

### Conflict xatolari

| HTTP | Code | Ma'nosi |
|---|---|---|
| 409 | `CONCURRENCY_CONFLICT` | Resource version mismatch (optimistic locking) |
| 409 | `LOAD_ALREADY_MATCHED` | Yukka allaqachon taklif qabul qilingan |
| 409 | `OFFER_ALREADY_EXISTS` | Bu yuk uchun bu driver/vehicle dan taklif mavjud |
| 409 | `MEMBER_ALREADY_EXISTS` | Foydalanuvchi allaqachon kompaniya a'zosi |
| 409 | `PLATE_ALREADY_EXISTS` | Davlat raqami allaqachon ro'yxatdan o'tgan |
| 409 | `AVAILABLE_VEHICLE_ACTIVE` | Bu vehicle uchun faol e'lon mavjud |
| 409 | `IDEMPOTENCY_KEY_CONFLICT` | Boshqa request body bilan bir xil key |

### Business rule violation

| HTTP | Code | Ma'nosi |
|---|---|---|
| 422 | `INVALID_STATE_TRANSITION` | State machine bo'yicha ruxsat etilmagan transition |
| 422 | `PUBLISH_REQUIREMENTS_NOT_MET` | Nashr uchun invariantlar bajarilmagan |
| 422 | `DRIVER_NOT_ELIGIBLE` | Haydovchi eligibility sharti bajarilmadi |
| 422 | `VEHICLE_NOT_ELIGIBLE` | Transport eligibility sharti bajarilmadi |
| 422 | `SELF_OFFER_NOT_ALLOWED` | O'z yukiga taklif berish taqiqlanadi |
| 422 | `LAST_OWNER_REMOVAL` | Kompaniyaning oxirgi ownerini olib tashlash mumkin emas |

### Rate limiting

| HTTP | Code | Ma'nosi |
|---|---|---|
| 429 | `RATE_LIMIT_EXCEEDED` | So'rov chastotasi oshirildi |
| 429 | `OTP_RATE_LIMITED` | OTP so'rash chastotasi oshirildi |
| 429 | `OTP_ATTEMPTS_EXCEEDED` | OTP urinish soni oshirildi |

`429` response `Retry-After` headerni o'z ichiga oladi.

### Provider/server xatolari

| HTTP | Code | Ma'nosi |
|---|---|---|
| 503 | `SERVICE_TEMPORARILY_UNAVAILABLE` | Tashqi provider vaqtincha mavjud emas |
| 500 | `INTERNAL_ERROR` | Server ichki xatosi |

`INTERNAL_ERROR` hech qachon stack trace yoki ichki tafsilotlarni ochmasligi kerak.

## Error code barqarorligi

- Error codlar API contractning bir qismi; o'zboshimchalik bilan o'zgartirilmaydi.
- Yangi code qo'shish backward compatible (client noma'lum codega tayyor bo'lishi kerak).
- Mavjud code semantikasini o'zgartirish breaking change.
