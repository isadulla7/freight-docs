# API dizayni

## Baza va shartnoma

- asosiy prefix: `/api/v1`;
- OpenAPI versiyalanadi;
- Flutter/Dart va TypeScript/Next.js uchun typed clientlar shartnomadan generatsiya qilinadi.

## Qoidalar

- katta feed va ro‘yxatlar uchun cursor pagination;
- filtering server tomonida;
- response faqat use case uchun zarur maydonlarni qaytaradi;
- foydali joylarda command va query application code’da ajratiladi;
- controller faqat transport modelini tarjima qiladi, biznes mantiqni saqlamaydi;
- xatolar barqaror, machine-readable formatda bo‘ladi;
- pulga ta’sir qiluvchi, high-risk va tanlangan mutation endpointlarda idempotency majburiy;
- permission tekshiruvi server tomonida va deny-by-default.

## Boshlang‘ich endpoint guruhlari

- `/auth`;
- `/users`;
- `/companies`;
- `/vehicles`;
- `/loads`;
- `/offers`;
- `/shipments`;
- `/chat`;
- `/notifications`.

## REST va realtime

Auth, profile, CRUD, search, offers va shipment query REST orqali. WebSocket faqat chat, offer notification, accepted offer, live status va kelajak live location kabi realtime foyda aniq bo‘lgan joylarda.

## Application use case

Command va querylar oddiy Kotlin classlari bilan aniq ifodalanadi; og‘ir CQRS framework qo‘shilmaydi. Masalan:

- command: `CreateLoadCommand`, `PublishLoadCommand`, `AcceptOfferCommand`, `CompleteShipmentCommand`;
- query: `GetLoadQuery`, `SearchLoadsQuery`, `GetLoadFeedQuery`, `GetShipmentQuery`.

Batafsil contract qarori: [ADR-0007](decisions/0007-openapi-contract.md).
