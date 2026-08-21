# Privacy by design

Qabul qilingan umumiy qaror: [ADR-0010](../architecture/decisions/0010-privacy-and-security-baseline.md).

Entity/table/field classification va `OPEN LEGAL DECISION`lar [data inventory](data-inventory.md)da authoritative tarzda saqlanadi.

Maxfiylik release oxiridagi hujjat emas, arxitektura talabidir.

Har bir yangi personal-data maydoni yoki feature uchun quyidagilar aniqlanadi:

1. Nima yig‘iladi?
2. Nima maqsadda yig‘iladi?
3. Qayerda saqlanadi?
4. Kim kira oladi?
5. Qancha vaqt saqlanadi?
6. Kirish qanday audit qilinadi?
7. Akkaunt o‘chirilganda yoki ma’lumot kerak bo‘lmaganda nima bo‘ladi?

## Data minimization

Verification hujjati yoki batafsil shaxsiy ma’lumot biznes oqimi talab qilmaguncha yig‘ilmaydi.

## Sezgir hujjatlar

Identity va haydovchi hujjatlari:

- faqat private object storage;
- doimiy public URL taqiqlanadi;
- har bir accessdan oldin authorization;
- zarur joyda qisqa umrli signed URL;
- privileged staff accessi audit qilinadi.

## Lokatsiya

Location access maqsadga xos bo‘ladi. Yaqin yuklarni topish va live shipment tracking ikki xil maqsad; bitta umumiy permission sifatida qaralmaydi.

## Consent

Consent versiyalanadi va audit qilinadi:

- `user_id`;
- `consent_type`;
- `policy_version`;
- `accepted_at`;
- `withdrawn_at`.

## O‘chirish

Akkaunt o‘chirish relational tarixni ko‘r-ko‘rona o‘chirishga tayanmaydi. Har bir data kategoriya uchun deletion, anonymization va qonuniy/biznes retention alohida belgilanadi.

## Huquqiy ko‘rib chiqish

Ushbu texnik hujjatlar huquqiy maslahat emas. Public production launchdan oldin Privacy Policy, Terms of Service, driver/company shartlari, consent/retention va kerak bo‘lsa cookie siyosati Uzbekistan-specific malakali mahalliy yurist tomonidan ko‘rib chiqiladi.
