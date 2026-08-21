# ADR-0008: Ichki eventlar va background ish

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Kontekst

Shipment yaratish, notification va audit kabi reaksiyalar modul chegaralarini buzmasdan bajarilishi kerak. MVP’da distributed brokerning operatsion narxi oqlanmagan.

## Qaror

- cross-module reaction uchun Spring application eventlari va zarur joyda Spring Modulith reliable event publication ishlatiladi;
- SMS, push, analytics, fayl processing va non-critical provider chaqiruvlari background bajarilishi mumkin;
- Kafka/RabbitMQ MVP baseline’ga kirmaydi;
- business logic event transportiga, Kafka yoki Firebase’ga bog‘lanmaydi;
- event handler idempotent va failure/retry xulqi aniq bo‘ladi.

## Evolyutsiya

Real throughput, delivery isolation yoki operatsion talab paydo bo‘lsa transport ADR orqali queue/brokerga almashtiriladi; domain event semantikasi saqlanadi.

## Tekshirish

- event publication/retry integration testlari;
- duplicate delivery testlari;
- provider unavailable bo‘lgandagi failure behavior testi.
