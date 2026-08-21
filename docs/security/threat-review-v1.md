# MVP architecture threat review — v1.0

Lightweight review architecture-level controlsni lock qiladi; implementation checklist va penetration test o‘rnini bosmaydi. Controls mavjud stack (Spring Security, PostgreSQL/PostGIS, Redis, private S3-compatible storage, reverse proxy/provider adapters) ichida; yangi service/framework talab qilinmaydi.

| Threat | Abuse/impact | Architecture mitigation | Validation / residual decision |
| --- | --- | --- | --- |
| OTP abuse | SMS flooding, brute force, cost/availability attack, user enumeration | phone/IP/device bo‘yicha layered rate limit va cooldown; per-challenge attempt cap/TTL; generic response; provider budget/circuit limits; raw OTP log/storage yo‘q | brute-force, resend, distributed-IP va enumeration tests; CAPTCHA/stronger challenge faqat abuse data talab qilsa |
| Account takeover | SIM swap, social engineering, recovery/phone-change abuse | short access token; device sessions; high-risk recovery/phone change alohida verified flow; session list/revoke; notification/audit | recovery policy implementationdan oldin open question; anomalous login signal baseline, no invented ML |
| Refresh token/session theft | stolen mobile storage/log token, replay | refresh token hash only; rotation and reuse detection; family/session revoke; device binding metadata; TLS; secure client storage; token log redaction | expiry/rotation/reuse/revoke tests; client secure-storage review |
| IDOR/resource authorization | UUID almashtirib boshqa load/offer/shipment/documentga kirish | UUID faqat identifier; every use case capability + ownership/assignment/participant + lifecycle; list/export scope; deny-by-default | other-user negative tests for each sensitive endpoint |
| Company cross-tenant access | requestdagi company IDni almashtirish, stale membership | active membership server-side; company-scoped role; resource company ID match; revoke invalidation; no client tenant trust | cross-company matrix tests, removed-member tests |
| Driver document exposure | broad admin read, over-broad response/export | separate `DRIVER_DOCUMENT_READ` and `DRIVER_VERIFY`; field minimization; private metadata; privileged view audit | verifier/support negative tests; audit presence; response schema review |
| Public object-storage URL leakage | permanent URL, bucket list, forwarded signed URL | private buckets; opaque keys; authorization before short-lived signed URL; no URL in event/log; expiry/content-disposition; provider access policies | anonymous/list access tests; URL expiry test; bucket policy review |
| Location leakage | precise pickup/vehicle position scraping, future live tracking abuse | purpose-separated collection; PostGIS server filter; return distance/coarse area unless actor needs exact point; result/radius/rate caps; no availability/live history in v1 | precision/role response tests; live tracking deferred with separate consent/retention threat review |
| Chat access leakage | conversation IDOR, WebSocket subscription without re-auth, push previews | participant check on REST history/send and WebSocket subscribe; server-derived recipients; generic push; private attachments; revoke/leave handling | non-participant REST/WS tests; push payload inspection |
| Offer race conditions | two winners/two shipments, cancel-vs-accept | load serialization/version; one transaction match+accept+reject; partial unique accepted offer; reliable `OfferAccepted`; unique shipment offer/load IDs | concurrent accept/cancel tests; duplicate event/reconciliation test |
| Mass scraping of load listings | competitor/privacy harvesting, high DB cost | minimized public fields, auth where needed, cursor+max page/radius, actor/IP/device rate limits, query cost/indexes, behavioral thresholds and temporary blocks | load test and enumeration test; stronger anti-bot control only after evidence |
| Rate-limit bypass/DoS | distributed sources, expensive geo/chat/auth queries | endpoint risk tiers; Redis counters; reverse-proxy/app limits; bounded input/radius/page/message/file; timeouts/provider circuit behavior; no unbounded query | quota/failure/load tests; infrastructure-specific values before deployment |
| Admin privilege abuse | support views documents/chat/audit or mutates lifecycle | no blanket admin; scoped permissions; separation of support/verification/security/audit; high-risk audit; exceptional state bypass absent v1 | admin-role negative matrix; periodic access review process before production |
| Audit log leakage/tamper | log becomes PII index, attacker erases evidence | append-only application API; minimal allowlisted fields; dedicated read/export permission; access itself audited; no raw payload/token/chat/location; protected backups | mutation denied, payload scanning, export scope tests; retention OPEN LEGAL DECISION |
| Secret leakage | credentials in Git/image/log/CI, provider key over-scope | environment/secret injection; no real `.env`; secret scanning; log redaction; least-privilege/rotatable provider keys; separate environments | repository/history/image/config scan; rotation runbook before production |

## Cross-cutting controls

- TLS non-local, secure headers/CORS/origin policy at deployment boundary;
- structured validation and bounded uploads/content types; malware/content scanning policy before accepting sensitive attachments in production;
- security events use correlation IDs without sensitive payload;
- auth, permission change, document view, verification and exceptional admin actions audited;
- dependency/container scanning and patch process per delivery baseline;
- backup restore access and production data in non-production prohibited;
- event consumers idempotent, retry observable, provider failure cannot mutate domain decision.

## Residual/high-priority follow-up

- phone change/account recovery, exact verification policy, chat moderation/reporting and legal retention cannot be safely invented; [open questions](../architecture/open-questions.md);
- public launch requires Uzbekistan-specific legal/privacy review;
- live GPS, payments/escrow, dispute and break-glass admin each require feature threat review and ADR before introduction.
