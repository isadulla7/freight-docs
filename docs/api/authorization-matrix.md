# Authorization matrix — API Contract v1

[Authorization modeli](../architecture/authorization-model.md) va [ADR-0015](../architecture/decisions/0015-authorization-and-audit.md)ga asoslanadi.

## Umumiy qoidalar

- Backend deny-by-default.
- Token/UI role permission source of truth emas.
- Har endpoint: authentication + capability + resource ownership/relationship + lifecycle state.
- Non-disclosure: agar actor resursning mavjudligini bilmasligi kerak bo'lsa, `404` qaytariladi (`403` o'rniga).

## Auth endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `POST /auth/otp/request` | Yo'q | — | — |
| `POST /auth/otp/verify` | Yo'q | — | — |
| `POST /auth/refresh` | Yo'q | — | — |
| `POST /auth/logout` | Ha | — | O'z sessiyasi |
| `GET /auth/sessions` | Ha | — | O'z sessiyalari |
| `POST /auth/sessions/{id}/revoke` | Ha | — | O'z sessiyasi |
| `POST /auth/sessions/revoke-all` | Ha | — | O'z sessiyalari |

## User endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `GET /users/me` | Ha | — | O'z profili |
| `GET /users/me/context` | Ha | — | O'z konteksti |
| `PATCH /users/me` | Ha | — | O'z profili |
| `GET /users/{userId}` | Ha | — | Public profil; relationship bo'lsa extended |
| `GET /users/me/driver-profile` | Ha | — | O'z haydovchi profili |
| `PUT /users/me/driver-profile` | Ha | — | O'z haydovchi profili |

## Company endpointlari

| Endpoint | Auth | Permission | Resource scope | Audit |
|---|---|---|---|---|
| `POST /companies` | Ha | — | Yaratuvchi owner bo'ladi | — |
| `GET /companies/{id}` | Ha | — | A'zo to'liq, boshqa public | — |
| `PATCH /companies/{id}` | Ha | `COMPANY_EDIT` | Company a'zosi | — |
| `GET /companies/{id}/members` | Ha | `COMPANY_MEMBER_READ` | Company a'zosi | — |
| `POST /companies/{id}/members` | Ha | `COMPANY_MEMBER_ADD` | Company a'zosi | Ha |
| `DELETE /companies/{id}/members/{mid}` | Ha | `COMPANY_MEMBER_REMOVE` | Company a'zosi | Ha |

## Vehicle endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `GET /vehicles` | Ha | — | O'z yoki company vehiclelari |
| `POST /vehicles` | Ha | `VEHICLE_CREATE` | O'z yoki company scope |
| `GET /vehicles/{id}` | Ha | — | Owner/company a'zosi to'liq; boshqa limited |
| `PATCH /vehicles/{id}` | Ha | `VEHICLE_EDIT` | Owner yoki company a'zosi |
| `POST /vehicles/{id}/deactivate` | Ha | `VEHICLE_EDIT` | Owner yoki company a'zosi |
| `POST /vehicles/{id}/images` | Ha | `VEHICLE_EDIT` | Owner yoki company a'zosi |

## Available Vehicle endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `GET /available-vehicles` | Ha | — | Public search (location minimized) |
| `POST /available-vehicles` | Ha | `VEHICLE_EDIT` | Vehicle owner/manager |
| `GET /available-vehicles/{id}` | Ha | — | Public (location minimized) |
| `POST /available-vehicles/{id}/close` | Ha | `VEHICLE_EDIT` | Vehicle owner/manager |

## Load endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `GET /loads` | Ha | — | Published feed (public, minimized) |
| `GET /loads/mine` | Ha | — | O'z/company yuklari |
| `POST /loads` | Ha | `LOAD_CREATE` | O'z yoki company scope |
| `GET /loads/{id}` | Ha | `LOAD_READ` | Owner to'liq; published public; boshqa non-disclosure 404 |
| `PATCH /loads/{id}` | Ha | `LOAD_EDIT` | Owner/company a'zosi; faqat DRAFT |
| `POST /loads/{id}/publish` | Ha | `LOAD_EDIT` | Owner/company a'zosi; DRAFT -> PUBLISHED |
| `POST /loads/{id}/cancel` | Ha | `LOAD_CANCEL` | Owner/company a'zosi; cancellable state |
| `POST /loads/{id}/images` | Ha | `LOAD_EDIT` | Owner/company a'zosi; faqat DRAFT |

## Offer endpointlari

| Endpoint | Auth | Permission | Resource scope | Audit |
|---|---|---|---|---|
| `GET /offers/mine` | Ha | — | O'z takliflari | — |
| `GET /loads/{id}/offers` | Ha | — | Load owner barchani; boshqa faqat o'zini | — |
| `POST /loads/{id}/offers` | Ha | `OFFER_CREATE` | Eligible driver + vehicle; o'z yukiga taklif taqiqlangan | — |
| `GET /offers/{id}` | Ha | — | Owner yoki load owner | — |
| `POST /offers/{id}/accept` | Ha | `OFFER_ACCEPT` | Load owner/company; concurrency-safe | Ha |
| `POST /offers/{id}/reject` | Ha | `OFFER_ACCEPT` | Load owner/company; PENDING | — |
| `POST /offers/{id}/withdraw` | Ha | `OFFER_WITHDRAW` | Offer creator; PENDING | — |

## Shipment endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `GET /shipments` | Ha | — | O'z tashishlari (shipper yoki driver) |
| `GET /shipments/{id}` | Ha | — | Shipment participant |
| `POST /shipments/{id}/status` | Ha | `SHIPMENT_STATUS_UPDATE` | Assigned driver (execution); shipper (completion) |
| `GET /shipments/{id}/status-history` | Ha | — | Shipment participant |

## Communication endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `GET /shipments/{id}/conversation` | Ha | — | Shipment participant |
| `GET /conversations/{id}/messages` | Ha | — | Conversation participant |
| `POST /conversations/{id}/messages` | Ha | — | Active conversation participant |
| `POST /conversations/{id}/read` | Ha | — | Conversation participant |

## Notification endpointlari

| Endpoint | Auth | Permission | Resource scope |
|---|---|---|---|
| `GET /notifications` | Ha | — | O'z bildirishnomalari |
| `POST /notifications/{id}/read` | Ha | — | O'z bildirishnomasi |
| `GET /notifications/preferences` | Ha | — | O'z sozlamalari |
| `PUT /notifications/preferences` | Ha | — | O'z sozlamalari |
| `POST /push-endpoints` | Ha | — | O'z qurilmasi |

## Reference data endpointlari

| Endpoint | Auth | Permission | Izoh |
|---|---|---|---|
| `GET /reference/vehicle-types` | Yo'q | — | Public reference |
| `GET /reference/body-types` | Yo'q | — | Public reference |
| `GET /reference/vehicle-capabilities` | Yo'q | — | Public reference |
| `GET /config/capabilities` | Yo'q | — | Feature availability |
| `GET /config/supported-currencies` | Yo'q | — | Valyuta ro'yxati |

## Cross-tenant himoya

- Company-scoped operatsiyalarda actor shu companydagi active member bo'lishi kerak.
- Boshqa company resource'iga kirish `404` (non-disclosure) qaytaradi.
- Company A a'zosi Company B resource'ini ko'ra olmaydi va o'zgartira olmaydi.
- UUID prediction bilan IDOR himoyalangan: permission + ownership check har safar.
