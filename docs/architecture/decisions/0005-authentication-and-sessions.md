# ADR-0005: MVP autentifikatsiya va sessiyalar

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: taqdim etilgan security baseline

## Kontekst

Mobil-first marketplace uchun O‘zbekistonda telefon raqami asosidagi kirish amaliy, ammo OTP abuse, token o‘g‘irlanishi va qurilmalarni boshqarish xavflari mavjud.

## Qaror

- MVP autentifikatsiyasi telefon raqami va OTP bilan;
- access token qisqa umrli;
- refresh token/session lifecycle server tomonida boshqariladi;
- sessiya har bir qurilma kesimida kuzatiladi va bekor qilinadi;
- OTP hamda auth endpointlarida rate limit va abuse himoyasi majburiy;
- raw OTP, access/refresh token yoki credential log qilinmaydi.

## Oqibatlar

- SMS provider port/adapter ortida bo‘ladi;
- OTP Redis’da faqat qisqa muddatli, qayta tiklanmaydigan autentifikatsiya holati sifatida saqlanishi mumkin;
- high-risk auth eventlar audit qilinadi;
- account recovery va telefon almashtirish oqimi implementationdan oldin alohida threat review talab qiladi.

## Tekshirish

- OTP brute-force va rate-limit integration testlari;
- token expiry, rotation, reuse detection va revoke testlari;
- loglarda auth secretlari yo‘qligini avtomatik tekshirish.
