# Holat mashinalari

Holat o‘tishlari UI taxmini bilan emas, backend domain qoidalari bilan bajariladi.

## Load lifecycle

```text
DRAFT -> PUBLISHED -> OFFERING -> MATCHED -> CLOSED
```

Terminal alternativalar:

- `DRAFT -> CANCELLED`;
- `PUBLISHED|OFFERING -> CANCELLED`;
- `PUBLISHED|OFFERING -> EXPIRED`.

## Offer lifecycle

```text
PENDING -> ACCEPTED
```

Terminal alternativalar:

- `PENDING -> REJECTED`;
- `PENDING -> WITHDRAWN`;
- `PENDING -> EXPIRED`.

Faqat bitta taklif yuk uchun g‘olib bo‘lishi mumkin. Acceptance race condition’ga qarshi transactional himoyalanadi.

## Shipment lifecycle

```text
CREATED
  -> DRIVER_ASSIGNED
  -> HEADING_TO_PICKUP
  -> AT_PICKUP
  -> LOADED
  -> IN_TRANSIT
  -> DELIVERED
  -> COMPLETED
```

Cancellation va dispute yo‘llari implementationdan oldin alohida qaror bilan belgilanadi.

## O‘zgarmas tarix

Har bir shipment status o‘zgarishi quyidagilar bilan immutable history yozadi:

- `shipment_id`;
- `previous_status`;
- `new_status`;
- `actor_id` yoki `system`;
- timestamp;
- ixtiyoriy reason/context.
