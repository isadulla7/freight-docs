# ADR-0010: Privacy va security baseline

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Qaror

- privacy by design va security by default feature definitionining bir qismi;
- authorization server-side, deny-by-default va least privilege;
- admin avtomatik ravishda barcha sensitive ma’lumotni ko‘rmaydi;
- identity/driver hujjatlari private object storage’da, permanent public URL’siz;
- nearby discovery va live tracking alohida location purpose/consent sifatida;
- yangi personal-data field bilan data inventory, purpose, access, retention, audit va deletion yangilanadi;
- consent versiyalanadi va audit qilinadi;
- sensitive operationlar audit qilinadi, raw token/OTP/password log qilinmaydi;
- har bir third-party provider uchun yuboriladigan data, purpose, region va retention inventari yuritiladi.

## Huquqiy chegara

Ushbu ADR huquqiy maslahat emas. Public production launchdan oldin Uzbekistan-specific privacy/legal hujjatlari malakali mahalliy yurist tomonidan ko‘rib chiqiladi.

## Tekshirish

- permission va object-storage access testlari;
- log/secret scan;
- data migration PR’ida inventory diff;
- sensitive feature Definition of Done review.
