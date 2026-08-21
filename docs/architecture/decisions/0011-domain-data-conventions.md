# ADR-0011: Money, vaqt va identifier konvensiyalari

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Qaror

- money floating point bilan saqlanmaydi;
- `Money` amount’ni integer/minor unit yoki yo‘qotishsiz integer representation va ISO-style currency code bilan saqlaydi;
- server timestamp UTC’da, foydalanuvchiga timezone asosida ko‘rsatiladi;
- boshlang‘ich primary timezone `Asia/Tashkent`;
- public/domain identifier UUID;
- ketma-ket database ID primary external identifier sifatida ochilmaydi.

## Oqibatlar

- UZS bilan birga keyingi USD/KZT/RUB kengayishi schema rewrite talab qilmaydi;
- vaqt bo‘yicha audit va xalqaro kengayish aniq bo‘ladi;
- external ID enumeration xavfi kamayadi.

## Tekshirish

- Money rounding/serialization testlari;
- UTC persistence va timezone conversion testlari;
- API schema’da UUID format check.
