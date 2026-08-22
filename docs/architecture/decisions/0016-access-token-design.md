# ADR-0016: JWT access token dizayni

- Holat: Qabul qilindi
- Sana: 2026-08-22
- Qaror egalari: Loyiha egasi
- Tegishli ADR: [ADR-0005](0005-authentication-and-sessions.md), [ADR-0015](0015-authorization-and-audit.md)

## Kontekst

`ResolveAuthenticatedPrincipal` uchun access token mexanizmi kerak. ADR-0005 refresh token/session lifecycle va OTP autentifikatsiyani belgiladi, lekin access token algoritmi, claimlari va validatsiya oqimini aniqlamaydi. Loyiha egasi quyidagi qarorni tasdiqladi.

## Qaror

- **Format:** JWT (JWS) — imzolangan, shifrlanmagan.
- **Algoritm:** EdDSA / Ed25519.
- **Access TTL:** 15 daqiqa.
- **Refresh token:** mavjud opaque/session mexanizm o'zgarishsiz qoladi (ADR-0005).
- **Majburiy claimlar:** `iss`, `aud`, `sub` (auth identity ID), `sid` (session ID), `jti` (unique token ID), `iat`, `exp`.
- **`kid` (Key ID):** majburiy — kelajakda bir nechta verification kalitlarni qo'llab-quvvatlash uchun.
- **Private key:** faqat runtime secret (`JWT_PRIVATE_KEY`), gitda saqlanmaydi.
- **Kelajakdagi ko'p kalitli qo'llab-quvvatlash:** `kid` orqali verification kalitlarni aniqlash imkoniyati saqlanadi.
- **Token claimlarida company/tenant/mutable permissionlar yo'q:** business authorization backendda runtime'da hal qilinadi, tokenga solinmaydi.
- **Server-side session tekshiruvi majburiy:** `ResolveAuthenticatedPrincipal` JWT imzosini tekshirgandan keyin server-side session holatini ham tekshiradi.
- **Revoked session:** bekor qilingan sessiya aks holda valid bo'lgan JWTni invalidatsiya qiladi.

## Variantlar

- **Stateless JWT (session tekshiruvsiz):** revoke qilingan sessiyani aniqlay olmaydi, xavfsizlikni pasaytiradi.
- **Opaque access token + server lookup:** har request uchun DB so'rov, JWT imzosi bilan solishtirganda sekinroq.
- **RSA/ES256:** keng tarqalgan, lekin Ed25519 tezroq va kalitlari kichikroq.
- **Tanlangan EdDSA + server-side session check:** tezkor imzo tekshiruvi + sessiya holatini kafolatlash.

## Oqibatlar

- `identity` modulida `AccessTokenIssuer` va `AccessTokenVerifier` internal servicelari kerak.
- Ed25519 kalit juftligi konfiguratsiyasi (`JWT_PRIVATE_KEY` env var).
- `Authenticate` va `RefreshSession` natijalarida access token qaytariladi.
- `ResolveAuthenticatedPrincipal` JWT claimlarni parse qiladi va `AuthSession` holatini tekshiradi.
- Nimbus JOSE+JWT (yoki ekvivalent) kutubxonasi build dependency sifatida qo'shiladi.

## Xavfsizlik va maxfiylik

- Private key faqat runtime secret — gitda, logda, error javoblarida chiqmaydi.
- Token claimlarida shaxsiy ma'lumot yo'q (telefon raqam, ism yo'q); faqat UUID identifikatorlar.
- 15 daqiqa TTL bilan o'g'irlangan tokenning hayot muddati cheklangan.
- Server-side session tekshiruvi revoke-dan keyin qolgan tokenlarni bloklaydi.
- `jti` claim har tokenni noyob qiladi (kelajakda replay himoyasi uchun).

## Tekshirish

- Access token issue/verify round-trip unit testlari.
- Expired token rad etilishini test qilish.
- Tampered/invalid imzo rad etilishini test qilish.
- Revoked session bilan valid JWT rad etilishini test qilish.
- `kid` claim mavjudligini test qilish.
- Private key loglarda chiqmasligini tekshirish.
