# ADR-0012: Domen aggregate va koordinatsiya chegaralari

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Canonical kontekst: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)
- Tafsilotlar: [Domen modeli](../domain-model.md), [Modul chegaralari](../module-boundaries.md)

## Kontekst

Backend implementation `Load`, `Offer` va `Shipment`ni bitta transaction modeliga birlashtirmasdan, company membership, vehicle availability, chat va status history uchun aniq consistency boundaryga muhtoj.

## Qaror

- `User`, `DriverProfile`, `Company`, `Vehicle`, `AvailableVehicle`, `Load`, `Offer`, `Shipment`, `Conversation`, `Message` va `Notification` alohida aggregate rootlar;
- `CompanyMember` Company lifecycleida, cargo/stop/requirement/pricing Load lifecycleida, assignment Shipment lifecycleida boshqariladi;
- unbounded message va history collection aggregate ichida load qilinmaydi;
- aggregate boshqa modul aggregate/entitysini faqat typed UUID orqali reference qiladi;
- `Load` marketplace listingi, `Shipment` accepted offerdan keyingi execution va ular birlashtirilmaydi;
- `AcceptOffer` kuchli cross-aggregate invariant sifatida marketplace orchestrationi, freight public `MatchLoad` API, bitta database transaction va reliable event publication orqali bajariladi;
- Shipment `OfferAccepted`dan idempotent yaratiladi; source master data o‘rniga faqat execution uchun zarur tarixiy snapshot saqlanadi;
- DDD ceremony oddiy reference/config data uchun majburiy emas.

## Variantlar

- Bitta katta `Load` aggregate ichida offer/shipment: oddiy ko‘rinsa ham contention, ownership va lifecyclelarni aralashtiradi.
- Har table alohida aggregate: transaction invariantlarini application servicega tarqatib yuboradi.
- Tanlangan pragmatic aggregate boundaries: lifecycle va concurrency bo‘yicha kesadi.

## Oqibatlar

- module/table ownership va mutation boundary aniq;
- cross-module orchestration faqat zarur kuchli invariantda sync transaction ishlatadi;
- event consumer duplicate deliveryga idempotent bo‘lishi kerak;
- source data o‘zgarganda shipment tarixini saqlash uchun minimal snapshot mapping talab qilinadi.

## Xavfsizlik va maxfiylik

Aggregate reference ID resource authorizationni almashtirmaydi. Sensitive child/document metadata root use case’i orqali scoped access oladi; API aggregate’ni to‘liq serialize qilmaydi.

## Tekshirish

- state-machine va aggregate invariant unit testlari;
- Spring Modulith boundary testlari;
- offer acceptance concurrency/integration testlari;
- duplicate `OfferAccepted` shipment-creation testi.
