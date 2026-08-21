# Pagination, filtering va sorting — API Contract v1

[ADR-0011](../architecture/decisions/0011-domain-data-conventions.md) va [api-design-standard](api-design-standard.md)ga asoslanadi.

## Cursor-based pagination

API offset-based pagination ishlatmaydi. Barcha collection endpointlari cursor-based pagination qo'llaydi.

### Nima uchun cursor?

- Offset + limit katta datasetlarda sekin (OFFSET N har safar N qatorni skip qiladi).
- Sahifalar orasida ma'lumot qo'shilsa/o'chirilsa offset noto'g'ri natija beradi.
- Cursor barqaror va samarali: indeks bo'yicha keyingi sahifani to'g'ridan-to'g'ri topadi.

### Request parametrlari

| Parametr | Turi | Default | Izoh |
|---|---|---|---|
| `limit` | integer | 20 | Sahifa hajmi; `1 ≤ limit ≤ 100` |
| `cursor` | string | — | Keyingi sahifa uchun opaque cursor |

Client birinchi so'rovda `cursor` yubormaydi. Keyingi sahifa uchun response dagi `nextCursor` qiymatini yuboradi.

### Response formati

```json
{
  "items": [ ... ],
  "nextCursor": "eyJpZCI6IjAxOTNhYjEyLTM0NTYtNzg5MC1hYmNkLWVmMDEyMzQ1Njc4OSJ9",
  "hasMore": true
}
```

| Field | Turi | Izoh |
|---|---|---|
| `items` | array | Joriy sahifa elementlari |
| `nextCursor` | string \| null | Keyingi sahifa cursori; `null` agar oxirgi sahifa |
| `hasMore` | boolean | Yana sahifa bormi |

### Cursor semantikasi

- Cursor **opaque** string — client ichki tuzilmasini bilmasligi va parse qilmasligi kerak.
- Server cursor ichida keyed position saqlaydi (masalan `id`, `createdAt`).
- Cursor faqat bir xil query parametrlari bilan ishlaydi. Agar client filter o'zgartirsa, yangi so'rov (`cursor` siz) kerak.
- Cursor muddati cheklangan emas, ammo underlying data o'zgarishi mumkin.
- Noto'g'ri yoki expired cursor `400 VALIDATION_ERROR` qaytaradi.

### Sahifa hajmi

- Default: `20`.
- Minimal: `1`.
- Maksimal: `100`.
- `limit` chegaradan tashqarida bo'lsa `400 VALIDATION_ERROR` qaytaradi.

## Filtering

### Filter parametrlari

Filter query parameter sifatida yuboriladi. Field nomlari `camelCase`:

```
GET /loads?pickupRegionId=01234567-...&minWeightKg=5000&vehicleTypeId=...
```

### Filter turlari

| Turi | Misol | Izoh |
|---|---|---|
| Exact match | `status=PUBLISHED` | Bitta qiymat |
| Multi-value | `status=PUBLISHED,MATCHED` | Vergul bilan ajratilgan; `OR` semantikasi |
| Range (min/max) | `minWeightKg=1000&maxWeightKg=5000` | Inklyuziv chegaralar |
| Boolean | `hasRefrigeration=true` | `true`/`false` string |
| Geo-radius | `pickupLatitude=41.31&pickupLongitude=69.28&radiusKm=50` | Markaziy nuqta + radius |
| Date range | `fromDate=2026-08-01&toDate=2026-08-31` | ISO 8601 `date` format |

### Filter qoidalari

- Noma'lum filter parametr **ignore** qilinadi (backward compatibility uchun).
- Bo'sh qiymatli filter ignore qilinadi: `status=` ≡ filter yo'q.
- Bir nechta filter `AND` semantikasi bilan birlashtiriladi.
- Multi-value bitta filter ichida `OR`: `status=PUBLISHED,MATCHED` → status PUBLISHED yoki MATCHED.
- Noto'g'ri qiymat (masalan enum da yo'q) `400 VALIDATION_ERROR` qaytaradi.

### Endpoint-specific filterlar

#### `GET /loads` (public feed)

| Parametr | Turi | Izoh |
|---|---|---|
| `pickupLatitude` | number | Yuklash nuqtasi latitude |
| `pickupLongitude` | number | Yuklash nuqtasi longitude |
| `deliveryLatitude` | number | Yetkazish nuqtasi latitude |
| `deliveryLongitude` | number | Yetkazish nuqtasi longitude |
| `radiusKm` | number | Qidiruv radiusi (km) |
| `vehicleTypeId` | uuid | Transport turi |
| `minWeightKg` | integer | Minimal og'irlik |
| `maxWeightKg` | integer | Maksimal og'irlik |
| `fromDate` | date | Boshlanish sanasi |
| `toDate` | date | Tugash sanasi |

#### `GET /loads/mine` (o'z yuklari)

| Parametr | Turi | Izoh |
|---|---|---|
| `status` | string | Status filter (multi-value) |
| `companyId` | uuid | Kompaniya bo'yicha |

#### `GET /available-vehicles` (public search)

| Parametr | Turi | Izoh |
|---|---|---|
| `latitude` | number | Qidiruv markazi latitude |
| `longitude` | number | Qidiruv markazi longitude |
| `radiusKm` | number | Qidiruv radiusi (km) |
| `vehicleTypeId` | uuid | Transport turi |
| `bodyTypeId` | uuid | Kuzov turi |
| `minCapacityKg` | integer | Minimal yuk sig'imi |

#### `GET /offers/mine` (o'z takliflari)

| Parametr | Turi | Izoh |
|---|---|---|
| `status` | string | Status filter (multi-value) |

#### `GET /shipments` (o'z tashishlari)

| Parametr | Turi | Izoh |
|---|---|---|
| `status` | string | Status filter (multi-value) |
| `role` | string | `SHIPPER` yoki `DRIVER` |

## Sorting

### Sort parametrlari

```
GET /loads?sortBy=createdAt&sortOrder=desc
```

| Parametr | Turi | Default | Izoh |
|---|---|---|---|
| `sortBy` | string | endpoint-specific | Saralash fieldi |
| `sortOrder` | string | `desc` | `asc` yoki `desc` |

### Sort qoidalari

- Har endpoint ruxsat etilgan sort fieldlarini belgilaydi.
- Noma'lum `sortBy` qiymati `400 VALIDATION_ERROR` qaytaradi.
- Default sort: `createdAt desc` (eng yangi birinchi).
- Geo-search endpointlarida default sort: `distance asc` (eng yaqin birinchi).

### Endpoint sort fieldlari

| Endpoint | Ruxsat etilgan fieldlar | Default |
|---|---|---|
| `GET /loads` | `createdAt`, `distance` | geo filter bo'lsa `distance asc`; aks holda `createdAt desc` |
| `GET /loads/mine` | `createdAt`, `updatedAt` | `createdAt desc` |
| `GET /available-vehicles` | `createdAt`, `distance` | geo filter bo'lsa `distance asc`; aks holda `createdAt desc` |
| `GET /offers/mine` | `createdAt` | `createdAt desc` |
| `GET /shipments` | `createdAt`, `updatedAt` | `createdAt desc` |

## Total count

API `totalCount` yoki `total` fieldini qaytarmaydi. Sabablar:

- `COUNT(*)` katta jadvalda sekin.
- Cursor pagination bilan `totalCount` semantik jihatdan noto'g'ri (real-time ma'lumot o'zgaradi).
- Client "Yana yuklash" / infinite scroll pattern ishlatishi kerak.

Agar kelajakda approximate count kerak bo'lsa, alohida endpoint yoki `X-Total-Count` header ko'rib chiqiladi.

## Empty result

```json
{
  "items": [],
  "nextCursor": null,
  "hasMore": false
}
```

Bo'sh natija `200` bilan qaytariladi, `404` emas.
