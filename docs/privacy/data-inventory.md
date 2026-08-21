# Shaxsiy ma’lumotlar inventari — Architecture v1.0 Lock

Ushbu inventar [Database ERD v1](../architecture/database-erd.md) table/fieldlarini privacy kategoriyaga map qiladi. Yangi personal-data field qo‘shilgan Flyway PR shu inventarni ham yangilaydi. Huquqiy muddatlar taxmin qilinmaydi.

## Kategoriyalar va handling policy

| Kategoriya | Purpose va storage | Authorized viewers | Audit | Retention status | Deletion/anonymization | Third-party exposure |
| --- | --- | --- | --- | --- | --- | --- |
| Public/non-sensitive operational (`O`) | load/vehicle type, capacity, public listingning minimized fields; PostgreSQL | public bo‘lishi explicit belgilangan published fields; qolganlari authenticated/scoped | mutation va abuse, har read emas | business record retention **OPEN LEGAL DECISION** | terminal record preserve; owner identity alohida anonymize; public searchdan olib tashlash | maps/pushga faqat zarur minimized data |
| Identity (`I`) | UUID, name, account/profile status; PostgreSQL | self, resource counterpartga zarur display subset, scoped support | privileged access/change | account + historical record talabi **OPEN LEGAL DECISION** | hard delete emas: mumkin joyda anonymize/pseudonymize; referential history saqlanadi | SMS/push/maps providerga faqat use case talabi |
| Contact (`I-contact`) | phone identity, stop contact name/phone; identity/freight PostgreSQL | self; active load/shipment participant; scoped support where necessary | privileged export/view | **OPEN LEGAL DECISION** | identity phone unlink/anonymize recovery policy bilan; stop contact history retention bilan | SMS provider; contactni push payload/chatga default qo‘ymaslik |
| Company/business (`B`) | company identity, membership, load/offer/shipment relation; PostgreSQL | active member role scope, counterpartga minimized business identity, scoped admin | membership/role/change/export | tax/business/shipment retention **OPEN LEGAL DECISION** | archive; personal member data anonymize, business transaction record preserve as legally required | providerga faqat notification/contract need |
| Vehicle (`V`) | plate, make/model, capacity, ownership, availability; PostgreSQL | manager/owner, eligible marketplace viewerga minimized fields, verifier | privileged view/change/verification | **OPEN LEGAL DECISION** | archive; plate/document data retention decision bilan redact/anonymize | object storage; maps only location use case |
| Location (`L`, high sensitivity) | pickup/delivery, availability current/destination, shipment stop snapshot; PostGIS `geography(Point,4326)` | nearby searchga distance/coarse result; exact point active owner/participant va purpose bilan | exact-location privileged/admin access va export | listing/execution purpose + **OPEN LEGAL DECISION**; v1 live/history collection yo‘q | closed availabilitydan unnecessary current point purge/generalize policy featuredan oldin; shipment/load history legal decision bilan | map/geocoding/route providerga minimized coordinates/address; region/retention provider inventoryda |
| Communication (`C`) | conversation, participant, message, notification/delivery; PostgreSQL/private object storage | active participant/recipient; scoped safety review faqat explicit future policy bilan | privileged access/export; normal participant read minimal telemetry | chat/notification **OPEN LEGAL DECISION** | account deletionda sender pseudonymization va content delete/redact policy legal/product review bilan | push providerga message body emas, generic notification + opaque resource ID preferred |
| Verification/document (`D`, very sensitive) | driver/vehicle evidence metadata PostgreSQL, binary private object storage | self where applicable; `DRIVER_DOCUMENT_READ`/verification scope only | every privileged view, upload, decision, signed URL | **OPEN LEGAL DECISION** | object + metadata controlled deletion/redaction after obligation; backups considered | object storage processor; no permanent public URL; provider region/retention recorded |
| Authentication/security (`A`, very sensitive) | phone auth identity, session/device token hashes, permissions, audit; PostgreSQL/Redis transient OTP | self session list; narrowly scoped security/admin; service components | high-risk auth/admin; audit read itself | session/OTP operational windows plus **OPEN LEGAL DECISION** for security/audit | OTP TTL; token revoke then purge policy; audit pseudonymize only if integrity/legal policy permits | SMS phone/OTP delivery; push endpoint provider token; never analytics/log payload |
| Future financial (`F`, not collected in v1) | future transaction ID, status, invoice; no v1 table/provider | payer/payee and scoped finance permission | all financial mutation/access | **OPEN LEGAL DECISION before collection** | payment/legal retention + subject-right policy before schema | payment provider inventory/DPA/region required before integration |

## Table va important-field mapping

| Module/table | Important fields | Category |
| --- | --- | --- |
| `identity.auth_identities` | `user_id`, `phone_e164`, verification/status | `A`, `I-contact` |
| `identity.auth_sessions` | user/device IDs, refresh-token hash, expiry/revoke/reuse metadata | `A` |
| `identity.auth_devices` | user ID, installation hash, last seen/revoked | `A` |
| `accounts.users` | UUID, display name, status, locale/timezone | `I` |
| `accounts.user_consents` | user, consent type/policy version, accepted/withdrawn evidence | `I`, `A` |
| `accounts.driver_profiles` | user ID, driver profile, verification status | `I`, `D` |
| `accounts.driver_documents` | type, private storage key, size/hash, expiry | `D` |
| `accounts.driver_verifications` | subject/verifier IDs, decision, reason/evidence version | `D`, `A` |
| `accounts.companies` | name, legal/business identifier, status | `B` |
| `accounts.company_members` | company/user relationship, active/removal state | `B`, `I` |
| `accounts.roles`, `permissions`, `role_permissions` | capability/scope configuration | `A`; role/permission codes themselves operational |
| `accounts.user_roles`, `company_member_roles` | user/member privilege assignments | `A`, `B` |
| `accounts.security_audit_log` | actor/subject/resource IDs, action/outcome/correlation/minimal metadata | `A` plus referenced category; exact payload allowlist |
| `fleet.vehicle_types`, `body_types`, `vehicle_capabilities` | codes/labels | `O` |
| `fleet.vehicles` | owner/company ID, plate, make/model/year, capacity/status | `V`, `I`/`B` relationship |
| `fleet.vehicle_capability_assignments` | vehicle/capability IDs | `V`, `O` |
| `fleet.vehicle_documents`, `vehicle_verifications` | storage metadata, verifier, decision | `D`, `V`, `A` |
| `fleet.available_vehicles` | vehicle ID, exact current/destination point, window | `L`, `V` |
| `freight.loads` | owner/company, cargo description/measure, price intent, status | `O`, `B`/`I`; free text may contain personal data and is validated/minimized |
| `freight.load_stops` | exact point/address, contact name/phone, schedule | `L`, `I-contact` |
| `freight.load_requirements`, `load_required_capabilities` | type/body/capacity/capability | `O` |
| `freight.load_images` | private storage metadata; image may expose cargo/person/location | operational but treat object as sensitive until reviewed |
| `marketplace.offers` | actor/carrier/vehicle IDs, amount/currency, message, status | `B`, `I`, `C` |
| `shipment.shipments` | participant/company/driver/vehicle IDs, accepted amount, lifecycle | `B`, `I`, `V` |
| `shipment.shipment_stops` | route/contact snapshot | `L`, `I-contact` |
| `shipment.shipment_status_history` | actor, status, reason, time | `B`, `A` |
| `communication.conversations` | shipment scope/status | `C`, `B` |
| `communication.conversation_participants` | user membership/read state | `C`, `I` |
| `communication.messages` | sender, body/attachment, time | `C`; attachment private |
| `communication.notifications` | recipient, type/source, minimized template data | `C`, `I` |
| `communication.communication_preferences` | user/channel/type choice | `C`, `I` |
| `communication.push_endpoints` | user/device, protected provider token | `A`, `I` |
| `communication.notification_deliveries` | endpoint/channel, attempt/error/delivery state | `C`, `A` |

## Non-database storage

| Store | Data | Required controls |
| --- | --- | --- |
| Redis | OTP challenge hash/state, attempt/rate-limit counters, transient session/cache metadata | TTL required, raw OTP yo‘q, key/payload minimization, not source of truth |
| Private object storage | driver/vehicle documents, load/chat attachments/images | private bucket, opaque key, encryption/provider controls, authorized short-lived signed URL, access audit for sensitive docs |
| Logs/metrics/traces | correlation, result/error code, aggregate type/opaque ID | phone/token/OTP/chat/document/exact location yo‘q; sampling/export access restricted; retention **OPEN LEGAL DECISION** |
| Backups | PostgreSQL va object metadata/content according to backup scope | encrypted where supported, restricted restore, deletion lag documented, retention **OPEN LEGAL DECISION** |

## Third-party inventory gate

Provider tanlanganda alohida inventory entry: legal entity, data fields, purpose, processing/storage region, retention, deletion, subprocessors, credential scope va fallback. Initial candidates (SMS, maps, object storage, push/FCM) architecture porti mavjudligi providerning privacy approvalini anglatmaydi.

## Open legal decisions

Uzbekistan retention periodi, lawful basis/consent, data-subject deletion/export, business/shipment record preservation, chat moderation/access va cross-border provider processing qualified local legal review talab qiladi. Production public launchdan oldin hal qilinadi; bu hujjat huquqiy maslahat emas.
