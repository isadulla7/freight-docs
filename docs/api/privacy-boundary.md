# Privacy boundary — API Contract v1

[ADR-0015](../architecture/decisions/0015-authorization-and-audit.md), [authorization model](../architecture/authorization-model.md) va [security baseline](../security/security-baseline.md)ga asoslanadi.

## Tamoyillar

- **Data minimization**: API faqat kerakli ma'lumotni qaytaradi; ortiqcha field ochilmaydi.
- **Non-disclosure**: ruxsatsiz actor resource mavjudligini bilmasligi kerak (`404` vs `403`).
- **Need-to-know**: ma'lumot faqat munosabat/relationship mavjud bo'lganda ochiladi.
- **Role-based visibility**: bir xil endpoint turli actorlar uchun turli fieldlarni qaytaradi.

## API field klassifikatsiyasi

### Klassifikatsiya darajalari

| Daraja | Ta'rifi | Misol |
|---|---|---|
| **Public** | Hamma ko'rishi mumkin | Yuk manzili (shahar darajasida), transport turi |
| **Authenticated** | Auth talab | Batafsil yuk ma'lumotlari, narx |
| **Relationship** | Munosabat mavjud bo'lganda | Telefon raqami, aniq manzil |
| **Owner-only** | Faqat egasi | Draft yuklar, shaxsiy sozlamalar |
| **Internal** | API da ochilmaydi | Database ID, internal flag |

### User fieldlari

| Field | Public | Auth | Relationship |
|---|---|---|---|
| `id` | Ha | Ha | Ha |
| `firstName` | Ha | Ha | Ha |
| `lastName` | Yo'q (faqat birinchi harf) | Ha | Ha |
| `phone` | Yo'q | Yo'q | Ha (shipment participant) |
| `avatarUrl` | Ha | Ha | Ha |
| `rating` | Ha | Ha | Ha |
| `createdAt` | Yo'q | Yo'q | Yo'q |

**Relationship** = shipment participant yoki company member.

### Company fieldlari

| Field | Public | Member |
|---|---|---|
| `id` | Ha | Ha |
| `name` | Ha | Ha |
| `logoUrl` | Ha | Ha |
| `memberCount` | Yo'q | Ha |
| `members` | Yo'q | Ha |
| `createdAt` | Yo'q | Ha |

### Vehicle fieldlari

| Field | Public (available) | Owner/member |
|---|---|---|
| `id` | Ha | Ha |
| `vehicleType` | Ha | Ha |
| `bodyType` | Ha | Ha |
| `capacityKg` | Ha | Ha |
| `plateNumber` | Yo'q | Ha |
| `images` | Ha | Ha |
| `location` | Yo'q (faqat shahar) | Ha (aniq) |
| `ownerId` | Yo'q | Ha |
| `createdAt` | Yo'q | Ha |

### Load fieldlari

| Field | Feed (public) | Owner | Offer qiluvchi |
|---|---|---|---|
| `id` | Ha | Ha | Ha |
| `title` | Ha | Ha | Ha |
| `cargo` | Ha (summary) | Ha (to'liq) | Ha (to'liq) |
| `stops` | Ha (shahar darajasida) | Ha (aniq manzil) | Ha (kabul qilingandan keyin aniq) |
| `price` | Ha (agar oshkor) | Ha | Ha |
| `shipper` | Ha (ism, reyting) | Ha | Ha |
| `shipper.phone` | Yo'q | Ha | Yo'q (shipment ga qadar) |
| `status` | Ha | Ha | Ha |
| `offersCount` | Yo'q | Ha | Yo'q |
| `createdAt` | Ha | Ha | Ha |

### Offer fieldlari

| Field | Load owner | Offer creator | Boshqa |
|---|---|---|---|
| `id` | Ha | Ha | Yo'q |
| `loadId` | Ha | Ha | — |
| `driver` | Ha (ism, reyting) | Ha | — |
| `vehicle` | Ha (turi, sig'imi) | Ha | — |
| `price` | Ha | Ha | — |
| `message` | Ha | Ha | — |
| `status` | Ha | Ha | — |
| `driver.phone` | Yo'q (shipment ga qadar) | Ha | — |

### Shipment fieldlari

| Field | Participant | Boshqa |
|---|---|---|
| `id` | Ha | Yo'q |
| `load` | Ha (to'liq) | — |
| `driver` | Ha (telefon bilan) | — |
| `shipper` | Ha (telefon bilan) | — |
| `status` | Ha | — |
| `statusHistory` | Ha | — |
| `conversation` | Ha | — |

Shipment faqat participants (shipper va driver) ko'radi. Boshqa actor `404` oladi.

## Non-disclosure qoidalari

### Resursni yashirish

Agar actor resource mavjudligini bilmasligi kerak bo'lsa:

```
# YOMON: resource mavjud, lekin ruxsat yo'q
HTTP 403 Forbidden

# YAXSHI: resource mavjud yoki yo'qligi noma'lum
HTTP 404 Not Found
```

Bu qoidalar qo'llaniladigan holatlar:

| Holat | Response |
|---|---|
| Boshqa company load ga kirish | `404` |
| Boshqa user draft load ni ko'rish | `404` |
| Boshqa shipment ga kirish | `404` |
| Boshqa company members ni ko'rish | `404` |
| Mavjud bo'lmagan resource | `404` |

### Istisnolar

`403` ishlatilishi mumkin bo'lgan holatlar:

| Holat | Response | Sabab |
|---|---|---|
| Auth bor, lekin company permission yo'q | `403` | Actor company member ekanligi ma'lum |
| Admin operatsiya, role yetarli emas | `403` | Actor tizimda ekanligi aniq |

## Geolocation minimization

### Available Vehicle

| Actor | Ko'rish darajasi |
|---|---|
| Public search | Faqat shahar/tuman nomi; aniq koordinatalar yo'q |
| Vehicle owner/manager | To'liq koordinatalar |

### Load stops

| Actor | Ko'rish darajasi |
|---|---|
| Feed (published) | Shahar darajasida (masalan "Toshkent — Samarqand") |
| Load owner | To'liq manzil va koordinatalar |
| Accepted offer driver | To'liq manzil va koordinatalar (shipment yaratilgandan keyin) |
| Pending offer driver | Shahar darajasida |

## Telefon raqami ochilish qoidalari

Telefon raqami shaxsiy ma'lumot. Ochilish faqat munosabat mavjud bo'lganda:

| Holat | Telefon ko'rinadimi |
|---|---|
| Public profilni ko'rish | Yo'q |
| Load feedda shipper | Yo'q |
| Offer berish paytida driver | Yo'q |
| Shipment participant (shipper ↔ driver) | Ha |
| Company member | Ha (company ichida) |

## Error response privacy

Error response ichki tafsilotlarni ochmasligi kerak:

```json
// YOMON
{
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "NullPointerException at LoadService.java:142",
    "details": { "stackTrace": "..." }
  }
}

// YAXSHI
{
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "Ichki server xatosi yuz berdi",
    "traceId": "req_abc123"
  }
}
```

Qoidalar:
- Stack trace hech qachon ochilmaydi.
- Internal class/method nomi ochilmaydi.
- Database table/column nomi ochilmaydi.
- File path ochilmaydi.
- `traceId` yordamida ichki loglardan tafsilot topiladi.

## Logging va PII

- API access log da request body loglanmaydi (PII bo'lishi mumkin).
- Phone number logda maskalanadi: `+998***1234`.
- Token (access/refresh) logda hech qachon ochilmaydi.
- Error log da faqat `traceId`, `userId`, `endpoint`, `status` saqlanadi.
- Audit log PII ni minimal saqlaydi (faqat `actorId`, `action`, `targetId`).

## GDPR/ma'lumot himoyasi

v1 da GDPR to'liq implement qilinmaydi, ammo arxitektura tayyor:

- Ma'lumot minimization: API faqat kerakli fieldlarni qaytaradi.
- Consent: hozircha OTP verification orqali implicit. Kelajakda explicit consent flow qo'shilishi mumkin.
- Data export/deletion: v1 da deferred ([open questions](../architecture/open-questions.md)).
- Retention: log va audit data retention server konfiguratsiyasi.
