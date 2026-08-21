# Database ownership — Architecture v1.0 Lock

Qaror [ADR-0013](decisions/0013-shared-database-ownership.md)da: bitta PostgreSQL database ichida har business modul o‘z schema va jadvallariga egalik qiladi.

## Ownership matrix

| Modul | PostgreSQL schema | Owned tables |
| --- | --- | --- |
| `identity` | `identity` | `auth_identities`, `auth_sessions`, `auth_devices` |
| `accounts` | `accounts` | `users`, `user_consents`, `driver_profiles`, `driver_documents`, `driver_verifications`, `companies`, `company_members`, `roles`, `permissions`, `role_permissions`, `user_roles`, `company_member_roles`, `security_audit_log` |
| `fleet` | `fleet` | `vehicle_types`, `body_types`, `vehicle_capabilities`, `vehicles`, `vehicle_capability_assignments`, `vehicle_documents`, `vehicle_verifications`, `available_vehicles` |
| `freight` | `freight` | `loads`, `load_stops`, `load_requirements`, `load_required_capabilities`, `load_images` |
| `marketplace` | `marketplace` | `offers` |
| `shipment` | `shipment` | `shipments`, `shipment_stops`, `shipment_status_history` |
| `communication` | `communication` | `conversations`, `conversation_participants`, `messages`, `notifications`, `communication_preferences`, `push_endpoints`, `notification_deliveries` |

Spring Modulith reliable event publicationning framework jadvallari platform infrastructure hisoblanadi va business modul ownershipini bermaydi. Flyway ularni aniq nomlangan platform migration bilan boshqaradi.

## Mutation va read qoidalari

- modul faqat o‘z schema jadvallarini repository/JPA/jOOQ write orqali o‘zgartiradi;
- cross-module command public application API orqali; cross-cutting reaction published event orqali;
- database trigger, cascade yoki stored procedure boshqa modul jadvalini o‘zgartirmaydi;
- boshqa schema ustida ad hoc JPA relation va repository taqiqlanadi;
- cross-module read composition ham public API/event-fed owned projection orqali; “read-only bo‘lsa mumkin” degan yashirin SQL join istisnosi yo‘q;
- Flyway migration bir nechta modul schema’sini o‘zgartirsa, ownership va rollout impact review qilinadi.

## Cross-module referential integrity

Pragmatic shared-database yondashuv:

1. Modul o‘z jadvalida boshqa modulning UUIDsini reference sifatida saqlashi mumkin.
2. Stable, current-lifecycle reference uchun physical foreign key ishlatiladi.
3. Cross-module FK `ON DELETE NO ACTION`/`RESTRICT`; `CASCADE`, `SET NULL` orqali boshqa modul lifecycleini yashirin boshqarish taqiqlanadi.
4. Reference yaratilishidan oldin target modul public API orqali actor scope va eligibility tekshiriladi. FK authorization emas, faqat integrityning oxirgi himoyasi.
5. Historical/audit record identitysi o‘chirilishi mumkin bo‘lsa, nullable actor ID + immutable minimal display snapshot yoki anonymized subject ishlatiladi; retention/deletion policy FK bilan tasodifan belgilanmaydi.
6. User/company/vehicle “delete” odatda status/archive/anonymization flow bo‘ladi. Hard delete faqat inbound reference yo‘qligi va retention qarori ruxsat berganda.
7. Kelajakda modul servisga ajratilsa, cross-schema FK migration ADR bilan application-level referencega almashtiriladi.

## Enforcement

- Spring Modulith/architecture test: boshqa modul persistence package importi yo‘q;
- migration review: schema owner, cross-module FK va delete action tekshiriladi;
- database runtime rolelarni schema bo‘yicha ajratish kelajak defense-in-depth; bitta app connection role MVP boundary testini almashtirmaydi;
- integration test: forbidden cascade yo‘q, reference yaratish public API’dan, orphan yaratish rad etiladi.
