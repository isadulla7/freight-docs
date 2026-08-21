# Backward compatibility — API Contract v1

[ADR-0007](../architecture/decisions/0007-openapi-contract.md) va [api-design-standard](api-design-standard.md)ga asoslanadi.

## Versioning strategiyasi

- Major version URL da: `/api/v1`, `/api/v2`.
- Minor/additive o'zgarishlar versiya oshirmasdan qo'shiladi.
- Mobile clientlar backend versiyasidan orqada qolishi mumkin — server yangi optional fieldlarni qo'shadi, ammo mavjud field semantikasini o'zgartirmaydi.

## Backward compatible (non-breaking) o'zgarishlar

Quyidagi o'zgarishlar **breaking emas** va versiya oshirmasdan amalga oshiriladi:

| O'zgarish | Misol |
|---|---|
| Yangi optional field qo'shish (response) | `Load` ga `estimatedDistanceKm` qo'shish |
| Yangi optional field qo'shish (request) | `CreateLoadRequest` ga `notes` qo'shish |
| Yangi endpoint qo'shish | `GET /loads/{id}/tracking` |
| Yangi enum qiymati qo'shish | `LoadStatus` ga `ARCHIVED` qo'shish |
| Yangi error code qo'shish | `LOAD_ARCHIVED` |
| Yangi query parametr qo'shish | `GET /loads?hasHazmat=true` |
| Yangi header qo'shish | `X-Feature-Flags` |
| Response da qo'shimcha field qaytarish | `Vehicle` da `lastInspectionDate` |

## Breaking o'zgarishlar

Quyidagi o'zgarishlar **breaking** va yangi major versiya talab qiladi:

| O'zgarish | Misol |
|---|---|
| Required field qo'shish (request) | `CreateLoadRequest` ga required `insuranceId` qo'shish |
| Field olib tashlash (response) | `Load` dan `price` olib tashlash |
| Field turini o'zgartirish | `weightKg` ni `integer` dan `string` ga |
| Field semantikasini o'zgartirish | `price.amount` ni kopeykadan so'mga o'zgartirish |
| Enum qiymatini olib tashlash yoki nomini o'zgartirish | `PUBLISHED` → `ACTIVE` |
| URL structurasini o'zgartirish | `/loads/{id}` → `/freight/{id}` |
| Error code semantikasini o'zgartirish | `LOAD_ALREADY_MATCHED` ma'nosini o'zgartirish |
| HTTP method o'zgartirish | `PATCH /loads/{id}` → `PUT /loads/{id}` |
| Authentication schemani o'zgartirish | Bearer → API key |

## Client mustahkamligi (robustness)

Clientlar quyidagi qoidalarga amal qilishi kerak:

### Noma'lum fieldlarni ignore qilish

Client response dagi noma'lum fieldlarni **ignore** qilishi kerak (crash qilmasdan). Bu serverga yangi field qo'shish imkonini beradi.

```dart
// YAXSHI: noma'lum field ignore qilinadi
final load = Load.fromJson(json); // yangi fieldlar skip qilinadi

// YOMON: strict parsing
if (json.keys.length != expectedKeys) throw Exception(); // crash
```

### Noma'lum enum qiymatlarini handle qilish

Client noma'lum enum qiymatiga crash qilmasligi kerak. `default` yoki `unknown` handler bo'lishi shart:

```dart
// YAXSHI
LoadStatus parseStatus(String value) {
  return LoadStatus.values.firstWhere(
    (e) => e.name == value,
    orElse: () => LoadStatus.unknown,
  );
}

// YOMON
LoadStatus parseStatus(String value) {
  return LoadStatus.values.firstWhere((e) => e.name == value); // crash
}
```

### Forward-compatible code generation

Dart va TypeScript client generatsiyasida:

- OpenAPI generator `unknownEnumValue` yoki `default` case support bo'lishi kerak.
- Response classlar `@JsonSerializable(ignoreUnannotated: true)` yoki shunga o'xshash flexible deserialization ishlatishi kerak.
- Generated code strict mode (unknown field = error) da ishlatilmasligi kerak.

## Deprecation policy

### Jarayon

1. Deprecated field/endpoint OpenAPI spec da `deprecated: true` annotation bilan belgilanadi.
2. Server `Sunset` HTTP header qaytaradi: `Sunset: Sat, 01 Mar 2027 00:00:00 GMT`.
3. Deprecation hujjatlashtiriladi: nima o'rniga ishlatish kerak va qachon olib tashlanadi.
4. Minimal deprecation window: **2 major mobile release yoki 90 kun** (qaysi uzunroq bo'lsa).
5. Window tugagandan keyin deprecated endpoint/field olib tashlanishi mumkin.

### OpenAPI deprecation misol

```yaml
/loads/{loadId}/legacy-status:
  get:
    deprecated: true
    x-sunset: "2027-03-01"
    description: |
      DEPRECATED: /shipments/{id}/status-history endpointini ishlating.
      Sunset: 2027-03-01
```

```yaml
LoadResponse:
  properties:
    legacyWeight:
      type: number
      deprecated: true
      description: "DEPRECATED: cargo.weightKg ni ishlating"
```

## Enum evolution

### Domain enum (state machine)

`LoadStatus`, `OfferStatus`, `ShipmentStatus` kabi domain enumlar state machine bilan bog'langan. Yangi qiymat qo'shish:

1. State machine diagrammasi yangilanadi ([state-machines.md](../architecture/state-machines.md)).
2. Transition table to'ldiriladi.
3. OpenAPI enum yangilanadi.
4. ADR yoziladi (agar semantik o'zgarish bo'lsa).
5. Client yangi qiymatni `unknown`/`default` handler orqali xavfsiz handle qiladi (yangilanmaguncha).

### Reference data

`VehicleType`, `BodyType`, `VehicleCapability` kabi reference data server tomonidan boshqariladi. Yangi qiymat qo'shish:

1. Database ga yangi entry qo'shiladi.
2. `GET /reference/*` endpointlari yangi qiymatni qaytaradi.
3. Client alohida API orqali reference data oladi va yangi qiymatlarni ko'rsatadi.
4. OpenAPI spec o'zgartirish talab qilinmaydi (reference data enum sifatida emas, alohida endpoint orqali beriladi).

## API versioning workflow

```
v1 (joriy)
  ├── additive o'zgarishlar (no version bump)
  ├── deprecated fieldlar (Sunset header)
  └── deprecated endpointlar (Sunset header)

v2 (kelajak, zarur bo'lganda)
  ├── breaking o'zgarishlar
  ├── v1 deprecated fieldlar olib tashlangan
  └── v1 parallel qo'llab-quvvatlanadi (deprecation window)
```

### Migration davri

v2 chiqarilganda:

1. v1 va v2 parallel ishlaydi.
2. v1 `Sunset` header bilan deprecated deb belgilanadi.
3. Deprecation window (minimal 90 kun yoki 2 mobile release) o'tgandan keyin v1 o'chirilishi mumkin.
4. Mobile force update v2 ga o'tishni majburlashi mumkin.
