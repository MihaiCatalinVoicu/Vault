---
title: Reservation-System
type: project
status: active
stack:
  - Spring Boot 3.5.5
  - Java 21
  - PostgreSQL 15 + logical replication
  - React 18 + Vite 7
  - Flutter (mobile)
  - PowerSync (local-first sync)
tags:
  - project/active
  - product/saas
  - product/horeca
created: 2026-04
updated: 2026-05-18
---

# Reservation-System

SaaS HoReCa multi-tenant pentru restaurant + hotel + spa + tourist hub. Monorepo cu backend Spring Boot + 3 frontend-uri (client public, admin staff, superadmin) + Flutter mobile + PowerSync local-first.

## On-disk

`C:\Personal\Reservation-Systems\Reservation-System\`

## Tenant comercial

Operat de **NOVENTRA SYSTEMS S.R.L.** (CUI 54594735, J2026028611002). Domain: `venuehubhq.com`.

## Stack & ports

| Surface | Port (dev) | Container | Rol |
|---|---|---|---|
| Backend Spring Boot | 8080 | `reservation_backend` | API + Flyway migrations |
| Admin / staff desktop | 3000 | `reservation_frontend` | Manager, POS, KDS |
| Client public (booking) | 3001 | `reservation_client_frontend` | Guest booking + manage + Tourist Hub |
| Superadmin | 3100 | `reservation_superadmin_frontend` | Cross-tenant intern |
| Hotel frontend (paralel) | 3002 dev / 3005 container | `hotel_frontend` | Track separat |
| Spa frontend (paralel) | 3004 | `spa_frontend` | Track viitor |
| Postgres | 55432 → 5432 | `postgres` | DB primary |
| PowerSync | (varies) | `powersync` | Local-first sync |
| MailHog | 8025 / 1025 | `mailhog` | Email test capture |

## Mobile

`mobile/` = melos workspace:
- `mobile_app/` — legacy monolithic Flutter app (`ro.venuehub.mobile`), currently shipping
- `apps/client_app/` — **NEW** public guest app target Play Store (`ro.venuehub.tourist`)
- `apps/staff_app/` — staff-only target Closed Track (`ro.venuehub.staff`), post-alpha
- `packages/shared/` — auth, HTTP, models, theme, FCM, deep_links

iOS native track separat la `ios/` (parking lot).

## Current execution

### Current state snapshot (2026-05-18)

Latest canonical vault snapshot: [[Reservation-System - State 2026-05-18]]

Short version:

- Accounting Ops are flow local/demo pentru Supplier Inbox, OCR local, Extraction Assist, matching/posting review-first, exporturi WIZCOUNT/SAGA/WinMentor, e-Factura preview/mock si SAF-T preview/local validator. Nu face live ANAF/SPV submission, nu face official SAF-T filing si nu posteaza contabil automat.
- Paid pilot billing este operational comercial prin flux extern/contabil/SPV, iar VenueHub urmareste intern customer profile, pilot order, invoice draft, line items, evidence si paid/unpaid.
- ANAF/e-Factura are lant tehnic controlat pana la readiness/ops: OAuth foundation, XML preview, operator-approved upload abstraction, manual status/evidence abstraction, readiness gates, manual retry si incident/release docs.
- Real ANAF TEST proof este inca **NO-GO**: lipsesc ANAF app registration/client credentials/SPV authority/private secrets/admin auth.
- Main repo head la snapshot: `779113f5 fix(billing): wire ANAF e-Factura clients for Spring`.

### Client app market readiness (started 2026-05-06)

10-sprint plan: [[2026-05-06 Client app market readiness sprint plan]] sau on-disk `docs/CLIENT_APP_MARKET_READINESS_SPRINT.md`.

Status:
- ✅ Sprint 1: BE sanity gate
- ✅ Sprint 2: Firebase + google-services.json (project `glass-trilogy-387110`)
- ✅ Sprint 3: keystore (redo done pe personal laptop, SHA256 nou: `79:71:92:6F:...`)
- ✅ Sprint 4: launcher icons + splash (placeholder programatic V italic)
- 🔴 Sprint 5: BLOCKED on personal laptop (need Flutter SDK + Android SDK install)
- ✅ Sprint 6: privacy URL-uri scurte (code done, deploy pending)
- ✅ Sprint 7: assetlinks.json with tourist entry
- ✅ Sprint 8B: listing pack doc ready (`docs/PLAY_CONSOLE_LISTING_PACK.md`)
- 🔴 Sprint 8A: KYC Play Console NOVENTRA SRL ($25 + 1-7 zile) — personal laptop
- 🔴 Sprint 9: smoke device cap-coadă — personal laptop
- 🔴 Sprint 10: upload AAB Internal Testing — personal laptop

Drum critic rămas: instalare SDK + build AAB + smoke + upload. KYC ruleaza paralel.

## Important context

- **CLAUDE.md** la rădăcina repo-ului — rulebook pentru Claude pe acest proiect
- **Sprint cadence**: format `Sprint XX-YY-NN — <title>`, un commit per sprint, build verified green ca DoD
- **Local-first POC**: PowerSync activated pe tenant 1 pentru POS + bill_splits writes
- **Friction event history**: useMemo după early return în `LocalFirstBillSplitsActions.tsx` (2026-05-06); JSDoc `*/` în comment block (2026-05-06)

## Cross-refs

- Decizie: [[2026-05-06 Client app market readiness sprint plan]]
- Reference: [[Stefanini laptop monitoring]] (cum afectează deploy)
- Project root on-disk: `C:\Personal\Reservation-Systems\Reservation-System\CLAUDE.md`
- Memorial docs in repo: `docs/00_PROJECT_TRUTH.md`, `docs/01_CURRENT_STATE.md`, `docs/03_RELEASE_LEDGER.md`, `docs/04_EXECUTION_CONTROL_TOWER.md`
