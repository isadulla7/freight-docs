# Authorization modeli — Architecture v1.0 Lock

Backend authorization authoritative, deny-by-default va least privilege. [ADR-0015](decisions/0015-authorization-and-audit.md) qarorni, [security baseline](../security/security-baseline.md) umumiy himoyani beradi.

## Authentication va authorization ajratilishi

`identity` kimligini isbotlaydi va qisqa umrli principal beradi: `UserId`, `SessionId`, `DeviceId`, authentication time/method. Token/UI role permission source of truth emas.

`accounts` global/company role-to-permission mapping va membershipni boshqaradi. Har business modul o‘z resource relationshipini biladi va permission bilan birga tekshiradi. Masalan `LOAD_EDIT`ning o‘zi yetarli emas: actor load owner yoki load company’sida ruxsatli active member bo‘lishi kerak.

```text
AuthenticatedPrincipal
  -> application use case
  -> accounts capability/membership check
  -> owning module resource relationship + state check
  -> allow or fail closed
  -> domain mutation
  -> sensitive/high-risk audit where required
```

Controller faqat principal/commandni use case’ga uzatadi. Annotation yoki UI hide/show yagona security gate bo‘lmaydi. Infrastructure requestni authenticate qiladi, lekin business ownership qarorini qabul qilmaydi.

## Role, permission va scope

- `Permission` — barqaror action capability code.
- `Role` — permissionlar to‘plami; UI label yoki numeric user type emas.
- `UserRole` — platform/global scope’dagi capability bundle.
- `CompanyMemberRole` — faqat bitta active company membership scope’ida.
- effective permission — active user + active membership + active role mapping; cached bo‘lsa invalidationga qaramay security-sensitive check source of truthga fail-closed qaytadi.
- direct per-user permission grant MVP’da yo‘q; istisno kerak bo‘lsa yangi policy/ADR, aks holda role sprawl va yashirin access paydo bo‘ladi.

Initial role nomlari seed/config bo‘lishi mumkin (`USER`, `DRIVER`, company `OWNER`/`MANAGER`/`DISPATCHER`, scoped admin rolelar), ammo code rol nomini business qaror sifatida tekshirmaydi; permission tekshiradi.

## Permission va resource policy matrix

| Permission | Scope va qo‘shimcha server-side condition |
| --- | --- |
| `LOAD_CREATE` | active user; individual owner yoki active company membership; company behalf uchun company-scoped permission |
| `LOAD_READ` | public feed faqat explicitly public/minimized published fields; private detail owner, eligible marketplace participant yoki shipment participant relationship bilan |
| `LOAD_EDIT` | load owner user yoki owning company active member; faqat state machine ruxsat bergan state |
| `LOAD_CANCEL` | load owner/scoped member; load cancellable state va winner yo‘q |
| `OFFER_CREATE` | verified/eligible driver capability, managed eligible vehicle, boshqa tomonning published load’i |
| `OFFER_ACCEPT` | load owner yoki owning company scoped member; pending offer; self-dealing/relationship policy va concurrency invariant |
| `OFFER_WITHDRAW` | ayni offer creator/carrier; pending offer |
| `VEHICLE_CREATE` | user o‘zi uchun yoki company membership scope’da; driver/company eligibility |
| `VEHICLE_EDIT` | vehicle owner user yoki manager company scoped member; verification-affecting change re-review policy bilan |
| `COMPANY_MEMBER_ADD` | target company active member va permission; assigned role company scope’ga mos; audit required |
| `COMPANY_MEMBER_REMOVE` | target company scope; self/last-owner invariant; audit required |
| `SHIPMENT_STATUS_UPDATE` | assigned driver faqat execution transitionlari; load owner/scoped member faqat completion confirmation; transition state-valid |
| `DRIVER_DOCUMENT_READ` | document ownerning self-service view yoki alohida verification/support scope; short-lived signed URL; har privileged view audit |
| `DRIVER_VERIFY` | verification admin scope; `DRIVER_DOCUMENT_READ` avtomatik imply qilinmaydi, zarur bo‘lsa rol ikkala permissionni explicit oladi; decision audit |

Additional high-risk platform permissions (`ROLE_ASSIGN`, `USER_BLOCK`, `SESSION_REVOKE`, audit read/export) alohida capability bo‘ladi; ular “ADMIN” boolean ichida yashirilmaydi.

## User, company va driver capabilities

- oddiy user bir vaqtning o‘zida cargo owner va company member bo‘lishi mumkin;
- company permission faqat requestdagi `CompanyId` membershipga mos bo‘lsa ishlaydi;
- driverlik faqat rol emas: `OFFER_CREATE` + active `DriverProfile` eligibility + vehicle eligibility birga talab qilinadi;
- verification state permission bermaydi; u permissiondan keyingi business eligibility gate;
- company memberning platform-global admin permissioni company role orqali berilmaydi.

## Admin capability boundaries

- Support capability public/minimized account va operational statusni ko‘rishi mumkin, driver document yoki auth token/session detailini avtomatik ko‘rmaydi.
- Verification capability faqat verification queue/evidence va decisionga; company/load mutationga emas.
- Security/session capability session revoke/security eventga; chat, document yoki shipment detailiga emas.
- Audit read/export alohida permission, purpose va audit bilan.
- “Super admin” normal operational yo‘l emas. Emergency break-glass keyinchalik kerak bo‘lsa short-lived approval, strong authentication va complete audit bilan alohida qaror talab qiladi.

## Resource ownership checks

Owning module minimal resource recordni oladi, keyin:

1. principal active va session valid;
2. required permission accounts public API orqali;
3. tenant/company scope active;
4. actor-resource relationship (owner, creator, assigned driver, participant);
5. resource lifecycle/eligibility;
6. field-level response minimization.

List/search query ham row-level scope qo‘llaydi; “detail endpoint himoyalangan” bo‘lishi feed/exportni ochiq qoldirishga ruxsat bermaydi. UUID IDORni to‘xtatmaydi.

## Sensitive resource policy

- Driver/vehicle document: private storage key, authorizationdan keyin qisqa signed URL, privileged view audit.
- Exact location: nearby discovery uchun coarse/minimized response; exact shipment location faqat active participant va purpose bilan.
- Chat: active conversation participant; participant condition har history/page/message sendda.
- Company: `CompanyId` requestdan kelgani bilan membership isbotlanmaydi.
- Audit log: dedicated read/export permission, PII-minimized result, accessning o‘zi audit.

## Audit strategy

`accounts.security_audit_log` centralized append-only security audit sink. Source module **qaysi** domain/security action auditga loyiqligini belgilaydi; accounts public `AppendSecurityAudit` recordni minimal contract bilan yozadi. Bu infrastructurega business decision bermaydi.

Majburiy audit:

- role/permission va company membership change;
- driver/vehicle sensitive document privileged view;
- verification decision;
- account block/unblock va high-risk session revoke/recovery;
- offer acceptance outcome/conflict;
- shipment status change (domain history canonical, security audit exceptional/admin action uchun);
- audit export/read va authorization-sensitive admin operation.

Record: actor, action, outcome, resource type/ID, company scope, UTC time, correlation ID, reason code. Raw request, chat body, OTP/token, document contents, exact location va keraksiz PII yozilmaydi. Application update/delete API yo‘q; retention `OPEN LEGAL DECISION`.

## Validation

- har permission uchun allow/deny/other-company/other-owner test;
- company cross-tenant va IDOR integration test;
- permission cache invalidation/revocation test;
- document signed-URL and audit test;
- state transition authorization matrix test;
- audit payload secret/PII allowlist test.
