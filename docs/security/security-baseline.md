# Security baseline

Qabul qilingan umumiy qaror: [ADR-0010](../architecture/decisions/0010-privacy-and-security-baseline.md).

## Autentifikatsiya

- MVP uchun telefon va OTP;
- qisqa umrli access token;
- refresh-token/session lifecycle;
- har bir qurilma kesimida sessiya tracking;
- OTP va auth endpointlarida rate limit.

Batafsil qaror: [ADR-0005](../architecture/decisions/0005-authentication-and-sessions.md).

## Avtorizatsiya

- barcha permission tekshiruvlari server tomonida;
- least privilege va deny-by-default;
- admin rollari scope bilan chegaralanadi;
- `admin hamma narsani ko‘radi` degan standart taxmin yo‘q.

Batafsil permission/resource matrix: [Authorization modeli](../architecture/authorization-model.md).

## Secretlar

- Git’da secret yo‘q;
- environment yoki secret manager injection;
- `.env.example` faqat nomlarni va xavfsiz misollarni saqlaydi.

## Audit

Quyidagi sensitive eventlar audit qilinadi:

- hujjatga kirish;
- permission o‘zgarishi;
- verification qarori;
- akkaunt bloklanishi;
- shipment status o‘zgarishi;
- high-risk auth event.

Password, raw token, OTP yoki keraksiz sensitive payload log qilinmaydi.

## Ma’lumot himoyasi

- lokal bo‘lmagan muhitlarda TLS;
- sensitive fayllar uchun private storage;
- infratuzilma qo‘llasa database backup shifrlanadi;
- restore jarayoni faqat sozlanmaydi, muntazam test qilinadi.

## Provider va audit chegarasi

Firebase, SMS, maps, object storage, analytics, crash reporting va keyingi payment providerlar uchun yuboriladigan data, purpose, processing region va ma’lum retention alohida inventarda yuritiladi. Provider domain mantiqqa bevosita bog‘lanmaydi.

MVP abuse case va architecture mitigations: [Threat review v1](threat-review-v1.md).
