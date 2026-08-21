# Authentication contract — API Contract v1

[ADR-0005](../architecture/decisions/0005-authentication-and-sessions.md) va [security baseline](../security/security-baseline.md)ga asoslanadi.

## OTP authentication flow

```text
Client                         Server
  |                              |
  |-- POST /auth/otp/request --> |  telefon raqamini yuboradi
  |<-- 200 { otpId, expiresAt }  |  SMS yuboriladi
  |                              |
  |-- POST /auth/otp/verify ---> |  otpId + code + deviceInfo
  |<-- 200 { accessToken,       |  yangi/mavjud user uchun tokenlar
  |          refreshToken,       |
  |          expiresAt, user }   |
  |                              |
  |-- Bearer accessToken ------> |  himoyalangan endpointlar
  |                              |
  |-- POST /auth/refresh ------> |  access token muddati tugaganda
  |<-- 200 { new tokens }       |  rotation: eski refresh ishlamaydi
  |                              |
  |-- POST /auth/logout -------> |  joriy sessionni tugatish
  |<-- 204                       |
```

## Endpointlar

| Endpoint | Auth | Izoh |
|---|---|---|
| `POST /auth/otp/request` | Yo'q | OTP so'rash; rate-limited |
| `POST /auth/otp/verify` | Yo'q | OTP tasdiqlash va login/register |
| `POST /auth/refresh` | Yo'q (refresh token body da) | Token yangilash, rotation |
| `POST /auth/logout` | Ha | Joriy session revoke |
| `GET /auth/sessions` | Ha | Foydalanuvchi sessiyalari ro'yxati |
| `POST /auth/sessions/{sessionId}/revoke` | Ha | Muayyan sessionni revoke |
| `POST /auth/sessions/revoke-all` | Ha | Barcha boshqa sessiyalarni revoke |

## Device va session metadata

Har autentifikatsiya `DeviceInfo`ni talab qiladi:
- `installationId` — qurilma installation identifikatori (hash saqlanadi).
- `platform` — `ANDROID`, `IOS`, `WEB`.
- `name` — qurilma nomi (ixtiyoriy, display uchun).

Server har sessiya uchun `AuthDevice` yaratadi yoki mavjudini bog'laydi. Sessiya ro'yxatida foydalanuvchi qurilmalarini ko'radi va boshqaradi.

## Security requirements

### OTP rate limiting

- Bir telefon raqamiga OTP so'rash chastotasi cheklangan (server konfiguratsiyasi).
- Ko'p muvaffaqiyatsiz urinishlardan keyin telefon vaqtinchalik bloklanadi.
- Response anti-enumeration uchun har doim `200` qaytaradi (telefon mavjud yoki yo'qligini oshkor qilmaydi).

### Brute force himoya

- OTP code urinish soni cheklangan (masalan 5 urinish).
- Har muvaffaqiyatsiz urinishda kechikish oshadi.
- Cheklovdan oshganda OTP invalidatsiya qilinadi.

### Anti-enumeration

- `POST /auth/otp/request` telefon mavjud yoki yo'qligini oshkor qilmaydi.
- Response formati va timing har doim bir xil.
- Error va success response bir xil field structurasida.

### Token security

- Access token qisqa umrli (server konfiguratsiyasi, masalan 15-30 daqiqa).
- Refresh token uzoq umrli, ammo rotation bilan: har refresh yangi juftlik beradi.
- Eski refresh token qayta ishlatilsa — **reuse detection**: barcha sessiya tokenlari revoke qilinadi (token theft ssenariysi).
- Raw token log yoki persistent storageda saqlanmaydi.
- Refresh token hash saqlanadi.

### Session revocation

- `POST /auth/logout` joriy sessionni revoke qiladi.
- `POST /auth/sessions/{sessionId}/revoke` boshqa qurilma sessionini revoke qiladi.
- `POST /auth/sessions/revoke-all` joriy sessiondan tashqari barchasini revoke qiladi.
- Account blocking barcha sessiyalarni avtomatik revoke qiladi.

### Device session authorization

- Har session bitta device va bitta user bilan bog'langan.
- Access token `UserId`, `SessionId`, `DeviceId` ma'lumotlarini o'z ichiga oladi.
- Server har requestda session validity tekshiradi.

## Deferred flowlar

Quyidagi flowlar v1 API contractda hali finalize qilinmagan ([open questions](../architecture/open-questions.md)):

- **Phone number o'zgartirish** — alohida threat review talab qiladi.
- **Account recovery** — proof, cooldown, notification flow aniqlanmagan.

Bu endpointlar kelajak versiyada qo'shiladi.
