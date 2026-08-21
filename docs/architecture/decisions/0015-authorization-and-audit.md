# ADR-0015: Capability-based scoped authorization va audit

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Tegishli ADR: [ADR-0005](0005-authentication-and-sessions.md), [ADR-0010](0010-privacy-and-security-baseline.md)
- Tafsilotlar: [Authorization modeli](../authorization-model.md)

## Kontekst

Bir user cargo owner, driver va bir necha company member bo‘lishi mumkin. UI role yoki bitta `ADMIN` flag resource ownership, tenant isolation va sensitive document accessini xavfsiz ifodalamaydi. High-risk qarorlarning minimal immutable audit izi ham kerak.

## Qaror

- `identity` authenticationni, `accounts` role/permission/membershipni, owning module resource relationship va lifecycle checkini bajaradi;
- backend deny-by-default; token/UI role authoritative permission source emas;
- role permission bundle, global yoki company scope’li; code role label emas permission capabilityni tekshiradi;
- access uchun capability + active scope/membership + resource relationship + domain state birga talab qilinadi;
- driver actionlari permissiondan tashqari driver/vehicle eligibility talab qiladi;
- admin sensitive permissionlari alohida; admin avtomatik hamma data accessiga ega emas;
- application use case kirishida authorization, mutationdan oldin; controller/infrastructure business ownership qarori qilmaydi;
- sensitive/high-risk actionlar `accounts.security_audit_log`ga minimal append-only record bilan;
- domain history (masalan shipment status) security auditni almashtirmaydi, ammo oddiy domain transitionni ikki marta to‘liq payload bilan yozish ham shart emas.

## Variantlar

- Hardcoded UI/user type: multiple capability va company scope’ni buzadi.
- Faqat RBAC: resource ownership/assignmentni ifodalamaydi.
- Tashqi policy engine: MVP uchun yangi service va operation complexity, talab isbotlanmagan.
- Tanlangan RBAC + resource relationship policy: mavjud stack ichida aniq va testable.

## Oqibatlar

- har use case explicit permission/resource policyga ega;
- role seed va permission migrationlar versioned;
- cache correctness security source of truthni almashtirmaydi;
- audit storage access/retention alohida himoya talab qiladi.

## Xavfsizlik va maxfiylik

Least privilege, cross-tenant deny, field minimization va document/location/chat uchun scoped checks majburiy. Audit raw payload, token, OTP, chat yoki documentni saqlamaydi; audit accessining o‘zi ham audited.

## Tekshirish

- permission/resource matrix unit va integration testlari;
- IDOR va cross-company negative tests;
- role revoke/cache testlari;
- audit immutability va payload allowlist testlari.
