# Security va abuse himoya — API Contract v1

[Security baseline](../security/security-baseline.md), [ADR-0005](../architecture/decisions/0005-authentication-and-sessions.md), [ADR-0015](../architecture/decisions/0015-authorization-and-audit.md)ga asoslanadi.

## Umumiy tamoyillar

- Defense in depth: bir nechta himoya darajasi.
- Deny-by-default: hech narsa ochiq emas, faqat oshkor ruxsat etilgan.
- Non-disclosure: ruxsatsiz actor resource mavjudligini bilmasligi kerak.
- Rate limiting: barcha endpointlar himoyalangan.
- Input validation: barcha input serverda validatsiya qilinadi, client validatsiyasiga ishonilmaydi.

## Abuse vektorlari va himoya

### 1. Authentication abuse

| Vektor | Himoya |
|---|---|
| OTP brute force | Urinish soni cheklangan (masalan 5); oshganda OTP invalidatsiya |
| OTP spam (SMS bombing) | Telefon raqamiga OTP so'rash chastotasi cheklangan |
| Phone enumeration | Response anti-enumeration: har doim bir xil format va timing |
| Token theft | Refresh token rotation + reuse detection → barcha sessiyalar revoke |
| Session hijacking | Token qisqa umrli; device binding; session revocation imkoniyati |
| Credential stuffing | OTP-based (parol yo'q); har urinish yangi OTP talab qiladi |

### 2. Resource abuse

| Vektor | Himoya |
|---|---|
| Spam load yaratish | Rate limiting + account-level throttle |
| Spam offer yuborish | Rate limiting + bitta load ga bitta driver dan bitta offer |
| Fake vehicle ro'yxatga olish | Rate limiting + kelajakda verification |
| Image spam/upload abuse | Fayl hajmi, formati, soni cheklangan; virus scan (kelajak) |
| Mass data scraping | Pagination limit; geo-search radius cheklangan; auth talab |
| Competitor intelligence | Location minimization; price detail faqat authorized actorga |

### 3. Business logic abuse

| Vektor | Himoya |
|---|---|
| O'z yukiga taklif berish | `SELF_OFFER_NOT_ALLOWED` business rule |
| Soxta kabul qilish (race condition) | Optimistic concurrency + idempotency |
| State manipulation | Server-side state machine; client transition so'rashi server tomonidan validatsiya |
| Cross-tenant access | Permission + ownership check har requestda; IDOR himoya |
| Price manipulation | Money integer representation; server-side calculation |
| Replay attack | Idempotency-Key; token expiration; nonce (kelajak) |

### 4. Infrastructure abuse

| Vektor | Himoya |
|---|---|
| DDoS | Rate limiting; infrastructure-level WAF (deployment scope) |
| Large payload | Request body size limit (server konfiguratsiyasi) |
| Slow loris | Connection timeout; request timeout |
| Header injection | Strict header parsing |

## Rate limiting strategiyasi

### Rate limit darajalari

| Daraja | Scope | Misol |
|---|---|---|
| Global | Server-wide | Umumiy throughput himoyasi |
| Per-IP | IP address | Anonim endpointlar (OTP request) |
| Per-user | Authenticated user | Autentifikatsiya qilingan endpointlar |
| Per-endpoint | Specific endpoint | Yuqori xavfli operatsiyalar |
| Per-resource | Specific resource | Bitta load ga takliflar soni |

### Endpoint kategoriyalari va limitlar

| Kategoriya | Endpointlar | Turi | Izoh |
|---|---|---|---|
| **Auth (strict)** | `/auth/otp/request`, `/auth/otp/verify` | Per-IP + Per-phone | Eng qat'iy; brute force himoya |
| **Auth (normal)** | `/auth/refresh`, `/auth/logout` | Per-user | O'rtacha |
| **Read (public)** | `/loads`, `/available-vehicles`, `/reference/*` | Per-IP / Per-user | Scraping himoyasi |
| **Read (private)** | `/loads/mine`, `/offers/mine`, `/shipments` | Per-user | O'rtacha |
| **Write (normal)** | `POST /loads`, `POST /vehicles` | Per-user | Spam himoyasi |
| **Write (critical)** | `POST /offers/{id}/accept` | Per-user + Idempotency | Eng yuqori himoya |
| **Upload** | Image/file upload endpointlari | Per-user | Hajm va son chegarasi |

### Rate limit response

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1693000000
```

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "So'rov chastotasi oshirildi. 30 soniyadan keyin qayta urinib ko'ring.",
    "traceId": "req_abc123"
  }
}
```

## Security controls per API group

### Auth endpointlari

- OTP request: per-IP va per-phone rate limiting; anti-enumeration (bir xil response).
- OTP verify: urinish limiti; urinish kechikishi (progressive delay); limit oshganda OTP invalidatsiya.
- Token refresh: rotation; reuse detection → sessiya revoke.
- Logout/revoke: faqat o'z sessiyalari.

### User endpointlari

- Profil o'qish: faqat public ma'lumot (boshqalar uchun).
- Profil yangilash: faqat o'z profili.
- Driver profil: faqat o'z haydovchi profili.

### Company endpointlari

- CRUD: capability-based permission (`COMPANY_EDIT`, `COMPANY_MEMBER_ADD`, `COMPANY_MEMBER_REMOVE`).
- Cross-tenant: boshqa company resource `404` qaytaradi.
- Audit: member qo'shish/olib tashlash audit log ga yoziladi.
- Last owner: oxirgi ownerni olib tashlash `LAST_OWNER_REMOVAL` bilan rad etiladi.

### Vehicle endpointlari

- CRUD: owner yoki company member; capability-based.
- Plate uniqueness: `PLATE_ALREADY_EXISTS` conflict.
- Deactivation: faqat owner/manager.

### Load endpointlari

- Yaratish: auth + `LOAD_CREATE` capability.
- Publish: invariantlar bajarilishi kerak (`PUBLISH_REQUIREMENTS_NOT_MET`).
- Cancel: faqat cancellable state da.
- Feed: published yuklar public (minimized); draft yuklar faqat ownerga.

### Offer endpointlari

- Yaratish: eligible driver + vehicle; o'z yukiga taklif taqiqlangan.
- Accept: load owner; concurrency-safe (idempotency + optimistic locking).
- Audit: accept operatsiyasi audit log ga yoziladi.

### Shipment endpointlari

- O'qish: faqat shipment participants (shipper va driver).
- Status update: role-based (driver execution davomida; shipper tugallashda).
- State machine: ruxsatsiz transition `INVALID_STATE_TRANSITION`.

### Communication endpointlari

- Conversation: faqat shipment participants.
- Messages: faqat active conversation participants.
- Cross-shipment: boshqa shipment conversation `404`.

### Notification endpointlari

- Faqat o'z bildirishnomalari va sozlamalari.
- Push endpoint: faqat o'z qurilmasi.

## Input validation summary

| Kategoriya | Validation |
|---|---|
| UUID | Format tekshiruvi (`uuid` regex) |
| Phone | E.164 format; faqat ruxsat etilgan country code |
| Coordinates | Latitude: -90..90; Longitude: -180..180 |
| Money amount | Integer ≥ 0; currency code supported ro'yxatda |
| Weight | Integer > 0; oqilona maksimal qiymat |
| String length | Min/max belgilangan; HTML/script injection strip |
| Enum | Ruxsat etilgan qiymatlar ro'yxatida |
| Date/time | ISO 8601 format; oqilona diapazon |
| File upload | Ruxsat etilgan MIME turlari; maksimal hajm |
| Pagination | `limit`: 1-100; `cursor`: format tekshiruvi |

## Security headerlari

Server quyidagi security headerlarni qaytaradi:

| Header | Qiymati |
|---|---|
| `X-Content-Type-Options` | `nosniff` |
| `X-Frame-Options` | `DENY` |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` |
| `Cache-Control` | `no-store` (authenticated endpointlar uchun) |
| `X-Request-Id` | Har response uchun unique |

## Monitoring va alerting

- Muvaffaqiyatsiz authentication urinishlari monitoring qilinadi.
- `429` response soni va chastotasi tracking qilinadi.
- `403`/`404` pattern anomaliya sifatida kuzatiladi (potential enumeration/scanning).
- Audit eventlar (offer accept, member add/remove) alohida saqlanadi.
- Suspicious pattern (bir IP dan ko'p OTP, bir user dan ko'p offer) alert trigger qiladi.
