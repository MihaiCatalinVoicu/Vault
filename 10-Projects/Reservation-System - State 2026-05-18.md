---
title: Reservation-System - State 2026-05-18
type: project-state
project: "[[Reservation-System]]"
status: active
created: 2026-05-18
updated: 2026-05-18
tags:
  - project/reservation-system
  - area/accounting-ops
  - area/anaf
  - area/billing
---

# Reservation-System - State 2026-05-18

## Executive state

VenueHub are acum un traseu comercial si fiscal tehnic controlat:

```text
facturare externa/manuala
-> billing customer + pilot order + invoice draft in VenueHub
-> ANAF/SPV OAuth foundation
-> XML e-Factura preview local
-> upload operator-approved, test-gated
-> status polling manual + evidence download/storage, test-gated
-> readiness gates + manual retry + incident/release docs
-> live proof NO-GO pana exista credentials/enrollment
```

Starea corecta: codul principal nu mai este blockerul dominant. Blockerul este operational:

- ANAF app registration / OAuth client id + secret.
- SPV/company authorization + certificat/contabil confirmat.
- token encryption key + OAuth state secret in runtime privat.
- admin auth pentru endpointurile protejate.
- callback URL de TEST accesibil si inregistrat.

Pana atunci verdictul ramane **NO-GO pentru real ANAF TEST proof**.

Repo state la snapshot:

- repo: `C:\Personal\Reservation-Systems\Reservation-System`
- branch: `main`
- head: `779113f5 fix(billing): wire ANAF e-Factura clients for Spring`
- vault note update: 2026-05-18

## Ce s-a facut zilele astea

### Accounting Ops

Accounting Ops a trecut de la runbook/export static la flow local/demo cu evidence, dar ramane review-first si safe-by-default.

Livrat / documentat:

- Supplier Inbox manual intake.
- Local OCR runtime cu Tesseract/OCRmyPDF/pdftotext.
- Extraction Assist cu review manual.
- Supplier Matching si Supplier Posting review-first.
- WIZCOUNT, SAGA si WinMentor pilot packets + walkthroughs + live cutover runbooks.
- e-Factura XML preview si ANAF/SPV mock/live-pilot docs pentru Accounting Ops.
- Incoming supplier e-Factura sync catre Supplier Inbox.
- SAF-T D406 generator preview.
- SAF-T local validator: disabled/mock/external-command.
- SAF-T monthly accountant review pack.
- Monthly close lock.
- Reconciliation/accounting demo smoke.
- Accounting Ops onboarding/help layer.
- Authenticated E2E demo smoke runner cu evidence redacted.

Docs principale in repo:

- `docs/accountingops/ACCOUNTING_OPS_E2E_DEMO_SMOKE.md`
- `docs/accountingops/LOCAL_OCR_RUNTIME.md`
- `docs/accountingops/ACCOUNTING_OPS_ONBOARDING.md`
- `docs/accountingops/EFACTURA_XML_PREVIEW.md`
- `docs/accountingops/EFACTURA_INCOMING_SUPPLIER_SYNC.md`
- `docs/accountingops/SAFT_D406_GENERATOR_PREVIEW.md`
- `docs/accountingops/SAFT_LOCAL_VALIDATOR.md`
- `docs/accountingops/SAFT_MONTHLY_PACK_ACCOUNTANT_REVIEW.md`
- `docs/accountingops/MONTHLY_CLOSE_LOCK.md`

Boundary:

- Nu face live ANAF/SPV submission.
- Nu face SAF-T filing oficial.
- Nu face cloud OCR.
- Nu foloseste LLM pentru extractie.
- Nu aproba automat supplier docs.
- Nu posteaza contabil automat.
- Evidence generat ramane local/ignored, nu in git.

### Paid pilot billing

S-a inchis blocajul comercial imediat: primul pilot platit poate fi facturat extern, fara claim fals ca VenueHub face upload live ANAF.

Livrat:

- `BILLING-READY-001`: workflow comercial pentru facturare prin contabil/SPV/tool extern.
- `EFACTURA-OUTBOUND-001`: modul intern pentru:
  - billing customer profile
  - pilot/commercial order
  - outbound invoice draft
  - line items + totals
  - external issue tracking
  - evidence references
  - paid/unpaid/overdue

Docs principale:

- `docs/billing/BILLING_READY_001_COMMERCIAL_READINESS.md`
- `docs/billing/EFACTURA_OUTBOUND_001_INVOICE_DRAFTS.md`
- `docs/billing/PAID_PILOT_BILLING_RUNBOOK.md`
- `docs/billing/PILOT_INVOICE_DATA_TEMPLATE.md`
- `docs/billing/PILOT_BILLING_LEDGER_TEMPLATE.md`
- `docs/billing/BILLING_EMAIL_TEMPLATES.md`

Boundary:

- VenueHub nu emite legal invoice singur.
- ANAF live upload nu este necesar pentru primul pilot platit.
- Repo-ul nu contine facturi reale, XML-uri reale sau tokenuri.

### ANAF / e-Factura compliance

S-a construit lantul tehnic controlat in sprinturi mici.

#### EFACTURA-ANAF-001 - Research

Stabilit modelul:

- OAuth prin `logincert.anaf.ro`.
- API upload/status/list/download.
- upload viitor cu raw XML + `standard=UBL`.
- `id_incarcare` ca pivot pentru status/evidence.
- open questions reale: enrollment, certificat, CIUS_RO, test environment.

Docs:

- `docs/billing/EFACTURA_ANAF_001_RESEARCH_SPIKE.md`
- `docs/billing/EFACTURA_ANAF_OPEN_QUESTIONS.md`

#### EFACTURA-ANAF-002 - OAuth/SPV connection foundation

Livrat:

- `anaf_spv_connections`
- `anaf_spv_connection_events`
- encrypted token storage abstraction
- signed OAuth state
- OAuth client abstraction
- admin connection endpoints
- Billing UI connection panel
- refresh / disconnect / event visibility

Boundary:

- real client off by default.
- tests mocked.
- tokens never returned in API.
- missing env vars do not break boot until flow is used.

Doc:

- `docs/billing/EFACTURA_ANAF_002_OAUTH_CONNECTION.md`

#### EFACTURA-ANAF-003 - XML generation

Livrat:

- UBL-style e-Factura XML preview din `OutboundInvoiceDraft`.
- preflight blockers/warnings.
- XML artifact DB.
- SHA-256 checksum.
- download endpoint.
- evidence reference.
- UI preview panel.

Boundary:

- no upload.
- no status polling.
- XML preview nu este legal submission proof.

Doc:

- `docs/billing/EFACTURA_ANAF_003_XML_GENERATION.md`

#### EFACTURA-ANAF-004 - Operator-approved upload

Livrat:

- `efactura_submissions`
- `efactura_submission_events`
- upload client abstraction.
- upload preflight.
- confirmation text `UPLOAD_EFACTURA`.
- statuses: `SUBMITTED`, `UPLOAD_FAILED`, `AUTH_FORBIDDEN`, `RATE_LIMITED`, `RETRYABLE_ERROR`.
- sanitized endpoints + UI upload section.

Boundary:

- real upload disabled by default.
- `SUBMITTED` nu inseamna `ACCEPTED`.
- commercial invoice status nu devine `PAID`.
- no status polling in acest sprint.

Doc:

- `docs/billing/EFACTURA_ANAF_004_OPERATOR_APPROVED_UPLOAD.md`

#### EFACTURA-ANAF-005 - Status + evidence

Livrat:

- manual status polling abstraction.
- message-list fallback abstraction.
- download client abstraction.
- status extensions: `PROCESSING`, `ACCEPTED`, `REJECTED`, `DOWNLOAD_AVAILABLE`, `DOWNLOADED`, failure states.
- `efactura_submission_files`.
- manual endpoints pentru poll/download/files.
- evidence metadata + checksum.
- UI poll/download.

Boundary:

- no scheduler.
- no auto polling.
- no auto retry.
- no resubmission.
- no legal acceptance guarantee.

Doc:

- `docs/billing/EFACTURA_ANAF_005_STATUS_AND_EVIDENCE.md`

#### EFACTURA-ANAF-006 - Production hardening

Livrat:

- readiness evaluator.
- connection health.
- token expiry warnings.
- retry candidates.
- manual retry service.
- `REUPLOAD_EFACTURA` pentru reupload periculos.
- audit summary.
- Billing Ops panel.
- release checklist.
- incident playbook.

Fix important in review:

- readiness nu are voie sa spuna `READY` cand real upload/status/download flags sunt off.

Boundary:

- no scheduler.
- no auto retry.
- no auto upload.
- no auto resubmission.
- no legal/tax guarantee.

Docs:

- `docs/billing/EFACTURA_ANAF_006_PRODUCTION_HARDENING.md`
- `docs/billing/EFACTURA_RELEASE_GATE_CHECKLIST.md`
- `docs/billing/EFACTURA_INCIDENT_PLAYBOOK.md`

## Live proof / access readiness

`EFACTURA-LIVE-PROOF-001` a produs **NO-GO controlat**, rezultat corect.

Ce a dovedit:

- backend health reachable.
- nu exista config/credentials ANAF TEST.
- OAuth real-client flag nu era explicit configurat.
- admin auth/token lipsea pentru endpoint checks.
- nu s-a creat connection.
- nu s-a facut OAuth callback.
- nu s-a stocat token.
- nu s-a facut upload/status/download call.

Docs:

- `docs/billing/EFACTURA_LIVE_PROOF_001_RUNBOOK.md`
- `docs/billing/runs/EFACTURA_LIVE_PROOF_001_20260517-233648.md`
- `docs/billing/EFACTURA_ANAF_ACCESS_001_CHECKLIST.md`
- `docs/billing/EFACTURA_ANAF_ENV_TEMPLATE.md`
- `docs/billing/EFACTURA_ANAF_APP_REGISTRATION_RUNBOOK.md`
- `docs/billing/EFACTURA_ANAF_SECRET_HANDLING.md`
- `docs/billing/runs/EFACTURA_LIVE_PROOF_001_NEXT_RUN_TEMPLATE.md`
- `scripts/check-efactura-anaf-readiness.ps1`

Ce trebuie obtinut:

1. ANAF app registration / OAuth client.
2. Client id + client secret stocate privat.
3. SPV/company/certificate/operator authorization confirmate cu contabilul.
4. Callback URL TEST valid si reachable.
5. Token encryption key + OAuth state secret.
6. Admin auth pentru protected checks.
7. Rerun `EFACTURA-LIVE-PROOF-001`.

## Runtime notes

Boot blocker post readiness merge a fost rezolvat:

- cauza: Spring nu putea alege constructorul pentru ANAF e-Factura clients, fiindca exista runtime constructor + package-private test constructor.
- fix: `@Autowired` explicit pe runtime constructors pentru upload/status/download clients.
- test: `AnafEFacturaClientSpringInstantiationTest`.
- validation: targeted ANAF/Billing sweep `49/49 PASS`.

Caveats locale:

- foloseste JDK 21 pentru Maven; JDK 25 rupe actualul Lombok/compiler path.
- dupa fix, boot check ajunge la PostgreSQL connectivity; daca Postgres nu asculta pe `localhost:5432`, boot pica din mediu.
- Hibernate `oid` warnings sunt schema drift/noise, nu cauza fatala din logul capturat.

## Ce se poate demo acum

Safe:

- paid pilot invoicing prin workflow extern/contabil/SPV.
- internal invoice draft tracking in VenueHub.
- local e-Factura XML preview + preflight.
- ANAF connection/readiness UI cu safe/mock/test-gated behavior.
- Accounting Ops demo smoke pe local/demo data.
- local OCR smoke daca Tesseract/OCR tools exista.
- SAF-T preview/local validator.

Nu se demo ca finalizat:

- real ANAF TEST connection.
- production ANAF upload.
- final ANAF acceptance.
- status polling real ANAF.
- response ZIP real de la ANAF.
- legal invoice submission by VenueHub.
- official SAF-T filing.
- automatic accounting posting.

## Next action

Nu mai mult cod fiscal acum. Urmatorul pas este access readiness:

```text
ANAF credentials + SPV authorization + private env + admin auth
-> rerun EFACTURA-LIVE-PROOF-001
```

Daca proof-ul devine GO:

```text
EFACTURA-LIVE-PROOF-002 - TEST upload/status/download proof
```

Daca ramane NO-GO, se rezolva punctual:

- callback invalid
- client secret invalid
- certificate/SPV rights missing
- test endpoint behavior unclear

