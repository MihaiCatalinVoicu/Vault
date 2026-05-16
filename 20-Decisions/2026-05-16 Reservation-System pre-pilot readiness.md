---
title: Reservation-System pre-pilot readiness assessment
type: decision
date: 2026-05-16
status: executed
impact: strategic
tags:
  - decision/product-readiness
  - decision/pre-pilot
  - product/reservation-system
---

# 2026-05-16 — Pre-pilot readiness assessment (3 zone critice)

## Context

Post pre-mortem [[2026-05-16 Pre-mortem Reservation-System]] — Tigers Launch-Blocking dezvăluiți. Utilizatorul confirmă focus pe Reservation-System ca "cele mai mari speranțe". Înainte de live (primul pilot), 3 zone trebuie clarificate:

1. **Accounting Ops** — pretins "gata azi"
2. **Fast Ops** — pretins "gata doar pentru restaurant, hotel + spa rămân"
3. **Full External Reservations** — necunoscut status

Verificare git + roadmap făcută 2026-05-16.

## Status real per zonă

### 1. Accounting Ops — ✅ GATA (production-quality)

**Verdict:** confirm utilizatorul. Una dintre cele mai complete zone din repo.

| Component | Status | Evidence |
|---|---|---|
| **e-Factura** | ✅ live pilot + incoming sync + xml preview | commits `863ebcf9`, `f8204036`, `847de0b6` |
| **ANAF SPV** | ✅ mock connection (ready for real cert) | `AccountingAnafSpvClient.java` + commit `ad72b08b` |
| **SAFT D406** | ✅ generator + local validator + monthly pack review | commits `bcf8c626`, `4e142931`, `01b69dd0` |
| **WinMentor** | ✅ pilot flow + monitoring + journal-live gate + candidate review + hardened export | 5 commits dedicate |
| **Supplier invoices** | ✅ extraction assist + invoice matching + controlled posting + accountant pack + inbox manual | commits `b181c005`, `52426565`, `b76fef4f`, `1f026335`, `fed833e0` |
| **Monthly close lock** | ✅ | commit `d1f34929` |
| **Reconciliation dashboard** | ✅ HEAD curent (`feature/accounting-ops-v7-4-reconciliation-dashboard`) | commit `33b42da0` |
| **Saga production pilot controls** | ✅ | commit `bc28f973` |

**Branch curent:** `feature/accounting-ops-v7-4-reconciliation-dashboard` — sugerează v7.4 in-flight, deci e activ. Per utilizator: ready azi.

**Docs canonice:** `docs/Accounting Ops Revizuit - Kernel Izolat + Jurnal Canonical.md` + `docs/Accounting Ops Unified HoReCa.md`.

**Pentru pilot:** ✅ acoperit pentru restaurant + hotel + spa cross-vertical. Real ANAF cert + WinMentor live wiring se face per-tenant la onboarding.

---

### 2. Fast Ops — ⚠️ PARITY PARȚIALĂ (restaurant complete, hotel/spa în progres)

**Verdict:** confirm utilizatorul. Cod există pentru toate 3 verticals, dar **UI parity + functional parity sunt active WIP**.

| Vertical | Service file | Status |
|---|---|---|
| **Restaurant** | `FastOpsRestaurantCommandService.java` (41 LOC) | ✅ Core complete, sprint închis în PARALLEL_LEDGER |
| **Hotel** | `FastOpsHotelCommandService.java` (60 LOC) | ⚠️ Recent endpoints adăugate (walk-in/move-room/maintenance-window per commit `3758b12f`) — funcțional dar UI parity incomplet |
| **Spa** | `FastOpsSpaCommandService.java` (60 LOC) | ⚠️ Recent endpoints (spa-walk-in per același commit) — funcțional dar UI parity incomplet |

**Branches active pe Fast Ops:**

- `feature/fastops-1to1-ux-functional-parity` ← UI parity hotel/spa
- `feature/fastops-backend-api-clean`
- `feature/fastops-dev-seed-clean`
- `feature/fastops-frontend-parity-clean`
- `feature/fastops-functional-parity-clean`
- `backup/fastops-local-wip` + `safety/fastops-local-wip-20260514-141429`

**Pentru pilot:** restaurant-only e ACCEPTABIL. Hotel + Spa fast ops parity NU blocant pentru primul pilot dacă pilot e restaurant-focused (cum sugerează `docs/Pricing and Plans - Restaurant First.md`).

> [!info] Recomandare: clearly mark hotel/spa fast-ops ca "post-Pilot-1 work" în roadmap. NU promite hotel/spa fast-ops la primul pilot dacă pilot-ul e restaurant — confuzia ar fi worse decât absența.

---

### 3. Full External Reservations — 🔄 FOUNDATION + IN-FLIGHT

**Verdict:** **mai mult e gata decât credea utilizatorul**, dar e POZIȚIONAT GREȘIT mental.

#### Ce este gata (FOUNDATION)

**Sprint EXT-RES-001 CLOSED** (acceptance report 2026-05-09 la `docs/sprints/EXT_RES_001_ACCEPTANCE_REPORT.md`):

- `ExternalReservationImportService` — layer persistență
- Entity + table foundation (V9010+)
- Bulk parser pipeline
- Inbox service pentru conflicte / review manual

**9 Controllers wired:**

- `AdminExternalReservationController` — base CRUD
- `AdminExternalReservationCommandCenterController` — command center
- `AdminExternalReservationInboxController` — inbox
- `AdminExternalReservationBulkController` — bulk import
- `AdminExternalReservationEmailController` — email pipeline
- `AdminExternalReservationInboundRouteController` — webhook routing
- `AdminExternalReservationAssignmentController` — table assignment
- `AdminExternalReservationAutopilotController` — auto-rules engine
- `AdminExternalReservationAutopilotOpsController` — autopilot ops

**Email ingestion EXT-EMAIL-001:**
- Operator forwardează email → sistem ingest → bulk parser → inbox
- HMAC-signed webhook EXT-EMAIL-002 (planned, per doc nota)
- Autopilot triggers EXT-AUTOPILOT-TRIGGERS-001 (chemat după email ingest dacă policy on)

#### Ce NU este gata (per strategic doc)

**Strategic doc:** `docs/strategy/RESERVATION_INTAKE_COMMAND_CENTER.md` planifică **15 PR-uri (~4-6 weeks)** pentru "Reservation Intake Command Center" complete:

- RES-CORE-001 (core mechanics)
- EXT-EMAIL-002 (HMAC webhook public + provider whitelist)
- EXT-RULES-001 (auto-rules engine declarative)
- EXT-PHONE-001 (quick phone intake)
- EXT-CONFLICT-001 (conflict resolution UI)
- EXT-HARDEN-001 (hardening pre-pilot)
- + ~9 alte PR-uri

Plus rescue branch `rescue/ext-bulk-001-local` — sugerează un re-attempt pe bulk (probably a recent setback).

#### Pozițonare comercială corectă (per strategic doc verbatim)

> "**Nu putem vinde încă un formular manual.** Patronul nu cumpără software ca să își mute munca dintr-un panou în altul. Cumpără ca să scape de haos."

**Două lucruri separate:**

1. **Ce putem controla 100% solo:** propriul booking widget, link-uri social/Google, email ingestion, bulk import, conflict inbox, auto-rules, quick phone intake, table assignment, reminders, analytics.
2. **Ce NU putem controla fără parteneriate/API:** IaLoc API real, Google Reserve Actions Center, OpenTable partner sync, Booking.com direct API.

Google Reserve cere feeds + Booking Server + sandbox/production review — NU e endpoint mic. **Acceptat: NU promite integrare reală OTA la primul pilot.**

#### Verdict pentru pilot

- ✅ **Foundation suficient pentru pilot:** email ingestion + bulk import + inbox + autopilot foundation. Patronul vede "rezervări din mai multe canale absorbite într-un singur loc".
- ⚠️ **GAP pentru "feel complete":** auto-rules engine elementar (EXT-RULES-001) + conflict UI elementar (EXT-CONFLICT-001) + quick phone intake (EXT-PHONE-001). Asta e 2-3 PR-uri targeted, NU 15.
- 🚫 **NU în scope pilot:** real Google Reserve / Booking.com API integrations.

> [!important] Recomandare strategic: NU "do all 15 PRs" pentru primul pilot. Selectează **3 PR-uri MVP** (RES-CORE-001 + EXT-RULES-001 + EXT-CONFLICT-001) și pune pe roadmap restul "pilot-dependent" (dacă pilot 1 cere, build; dacă nu, post-pilot).

---

## Gaps reziduale (post-evaluare cele 3 zone)

| Gap | Severity | Mitigation |
|---|---|---|
| Hotel + Spa Fast Ops UI parity incomplet | 🟡 Medium — NU blocant dacă pilot-ul e restaurant-only | Mark explicit "restaurant-only pilot" în messaging (vezi E2 din pre-mortem) |
| External Reservations 3 PR-uri MVP rămase (rules + conflict + phone) | 🟡 Medium — face produsul "feel complete" | Decide: build NOW (2 săpt) sau accept ca pilot 1 limitation |
| Tigers Launch-Blocking de la [[2026-05-16 Pre-mortem Reservation-System]] (Hetzner SPOF, GDPR ops, Fiscal RO, backups, runway) | 🔴 Critical — orthogonal de feature completeness | Acoperite în decision file pre-mortem |

---

## Decizii forțate

### D1. Pilot vertical scope (forțând E2 din pre-mortem)

**Decizie:** Pilot 1 = **restaurant-only**. Hotel + Spa = "available but not primary pitch", visible în demo doar dacă tenant pilot are interes hibrid.

**Rationale:** restaurant Fast Ops complete + accounting cross-vertical complete + external reservation foundation complete. Hotel + Spa fast-ops parity nepilot-blocker.

### D2. External Reservations — 3 PR-uri MVP DOAR

**Decizie:** RES-CORE-001 + EXT-RULES-001 + EXT-CONFLICT-001 (2-3 săpt work). NU full 15-PR Command Center.

**Rationale:** demo poate spune "rezervări din mai multe canale → 1 inbox cu auto-rules pentru routing". Suficient pentru "wow factor" demo + functional pentru pilot real.

### D3. Mobile app launch timing (forțând E4 din pre-mortem)

**Decizie:** **PAUSE** Sprint 5/8A/9/10 (Play Store readiness) **până când un prospect concret cere mobile**. Effort ~40-60h ar fi mai bine investit în T1 prospecting + D2 (3 PR-uri MVP external).

**Rationale:** "Construiești o sală goală pentru clienți care nu sunt invitați" — pre-mortem insight. Confirmation paying pilot că mobile e wanted = pre-requisite pentru Sprint 5+.

### D4. Demo video pentru outreach (NEW — vezi reference)

**Decizie:** prioritize demo video înainte de cold outreach mass. Vezi [[Demo video strategy]] pentru playbook complet.

**Effort:** 1-2 zile (Playwright deterministic + OBS + ElevenLabs TTS).

---

## Status sumar — ce e gata azi pentru pilot

| Component | Status | Ready pentru pilot? |
|---|---|---|
| Backend Spring Boot + Postgres | ✅ Live pe `89.167.121.99` | ✅ DA |
| Restaurant Fast Ops | ✅ Complete | ✅ DA |
| Restaurant POS + bills + floor board | ✅ Complete (Sprint 8.x) | ✅ DA |
| Accounting Ops (eFactura, ANAF SPV, SAFT, WinMentor) | ✅ Complete | ✅ DA (per tenant cert needed) |
| External Reservations foundation (ingest + inbox + autopilot) | ✅ Foundation | ⚠️ Parțial (3 PR-uri MVP rămase) |
| Hotel Fast Ops UI parity | ⚠️ WIP (multiple branches) | ❌ NU în pilot 1 scope |
| Spa Fast Ops UI parity | ⚠️ WIP | ❌ NU în pilot 1 scope |
| Mobile client_app (Play Store) | ⚠️ Foundation only (Sprint 1-4/6/7 done) | ❌ PAUSE până pilot cere |
| Hetzner SPOF mitigation | ❌ NU | 🔴 BLOCKING pentru pilot (vezi pre-mortem T2) |
| Postgres backup automation | ❌ NU | 🔴 BLOCKING pentru pilot (vezi pre-mortem T3) |
| GDPR ops rehearsal | ❌ NU | 🔴 BLOCKING pentru pilot (vezi pre-mortem T4) |
| Fiscal RO real (AMEF cert) | ❌ NU (NoOp stub) | 🔴 BLOCKING pentru first paying tenant (vezi pre-mortem T5) |

**Verdict consolidat:** **product feature-wise pre-pilot ready** (restaurant scope). **Operational pre-pilot blockers** = 4 critical (Hetzner SPOF, backups, GDPR, fiscal). **Commercial pre-pilot blocker** = lipsa prospect identificat (pre-mortem T1).

## Cross-refs

- Pre-mortem master: [[2026-05-16 Pre-mortem Reservation-System]]
- Sibling pre-mortem: [[2026-05-16 Pre-mortem Trading Bots]]
- Video strategy: [[Demo video strategy]] (reference)
- Project file: [[Reservation-System]]
- Mobile readiness: [[2026-05-06 Client app market readiness sprint plan]]

## Standing decisions (NU re-decide)

- Pilot 1 = restaurant-only
- External Reservations pilot scope = foundation + 3 PR-uri MVP (rules + conflict + phone)
- Mobile launch = PAUSE până prospect cere
- Demo video = PRE-REQUISITE pentru outreach mass
