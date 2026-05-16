---
title: Pre-mortem Reservation-System
type: decision
date: 2026-05-16
status: executed
impact: strategic
horizon: 6-months
tags:
  - decision/risk-analysis
  - decision/strategic
  - product/reservation-system
  - product/saas
---

# 2026-05-16 — Pre-mortem Reservation-System (VenueHub SaaS)

Aplicat skill-ul `borghei-pre-mortem` pe scope-ul `C:\Personal\Reservation-Systems\Reservation-System\`. Cadrul Klein 2007 + clasificare Tigers/Paper Tigers/Elephants. Horizon **6 luni** (orizont realist pentru SaaS: ai prins un paying customer, infrastructure-ul a ținut, GDPR fine, codebase shippable).

## Scope

SaaS HoReCa multi-tenant operat de [[Reservation-System|NOVENTRA SYSTEMS S.R.L.]]:
- Backend Spring Boot 3.5.5 / Java 21 / Postgres 15 + logical replication
- 3 frontends web (`:3000` staff, `:3001` client public, `:3100` superadmin) + parallel `:3002/:3005` hotel + `:3004` spa
- Flutter mobile (legacy `mobile_app/` + new `client_app/` Play Store target)
- PowerSync local-first POC pe tenant 1
- Deploy: Hetzner `89.167.121.99`, domain `venuehubhq.com`

## Phase 1 — Failure framing

> *"E mid-noiembrie 2026. Reservation-System a eșuat material. NU avem niciun pilot plătit sau l-am pierdut pe primul; app-ul Play Store retras; Hetzner a căzut zile multiple cu pierderi de date; ANSPDCP a flagged ceva GDPR; client review devastator; ANAF audit fiscal/AMEF la primul tenant; codebase prea complex să mai shippezi; sau bandwidth s-a evaporat în alte proiecte. Ce a mers prost?"*

## Phase 2-5 — Risk Registry

### 🔴 Tigers (Launch-Blocking — must mitigate NOW)

#### T1. **NU există pilot identificat concret cu nume + data semnării**

- **Evidence:** documents extensive pentru tehnic (`PILOT_EXECUTION_PLAYBOOK`, `First Assisted Pilot Checklist`, `Paid Pilot Dry Run Script`, `Pricing Sheet`), DAR niciun nume restaurant interesat documented, nicio data target, nicio negociere activă. Toate sprinturile tehnice presupun un destinatar care nu există încă.
- **Failure mode:** 6 luni de azi: 100+ ore investite în Play Store + folio hotel + tourist hub + local-first. Zero MRR. Motivation erodată. Project decay.
- **Mitigation:** lista 10 restaurante target RO + outreach 5/săpt + first conversation booked în max 4 săpt
- **Owner:** Mihai
- **Decision date:** 2026-06-13 (4 săpt)

#### T2. **Single-server Hetzner SPOF totală**

- **Evidence:** `89.167.121.99` singur server, docker-compose single-host, deploy via SFTP + sudo install. Niciun HA, niciun failover automation.
- **Failure mode:** hardware fail / ransomware / network outage Hetzner 6-12h → toți clienții reali down → SLA breach + reputation kill.
- **Mitigation:** (a) Hetzner snapshot daily automated; (b) DR runbook cu RTO target documentat; (c) Cold standby region prepared (Hetzner Falkenstein vs Helsinki)
- **Owner:** Mihai
- **Decision date:** Înainte de prima factură către primul pilot

#### T3. **Postgres backups untested (restore drill missing)**

- **Evidence:** `docker-compose.yml` Postgres fără backup volume mount automatic confirmat. PowerSync logical replication = NOT backup. Niciun restore drill în `docs/03_RELEASE_LEDGER.md`.
- **Failure mode:** corruption / migration bug / disk full / ransomware → restore tentative reveals "we don't have clean backup". Tenant data lost → GDPR Art. 33 breach + Art. 32 inadequate security → fine.
- **Mitigation:** (a) `pg_dump` cron daily → S3/B2/Backblaze; (b) Restore drill QUARTERLY pe staging DB; (c) Document RTO/RPO target
- **Owner:** Mihai
- **Decision date:** 2 săpt

#### T4. **GDPR ops paper-only (DSAR + breach + DPIA ne-testate)**

- **Evidence:** `docs/legal/PRIVACY_POLICY_RO.md` exists (draft, nereviewed lawyer per cod nota). `DPA_RO.md` + DPO `dpo@venuehubhq.com` declarat. Dar **niciun DSAR processed manual end-to-end pentru sanity**, breach plan = doc text nu rehearsed.
- **Failure mode:** client EU face DSAR ("dați-mi datele mele"). 30 zile deadline. Effort: extract 8 tabele tenant-scoped, format JSON/CSV, audit access. Eficiență 0 prima dată → miss deadline → ANSPDCP complaint → investigation → fine.
- **Mitigation:** (a) Rehearsal DSAR end-to-end cu mock user (4h); (b) Breach notification template letter; (c) DPIA scris pentru high-risk processing
- **Owner:** Mihai (+ lawyer review 1h)
- **Decision date:** 4 săpt

#### T5. **Fiscal RO NoOp doar (AMEF, e-Factura, ANAF)**

- **Evidence:** `docs/01_CURRENT_STATE.md` sprint 8.4 Fiscal Adapter "NoOp + GenericSerial stub" — explicit **blocker comercial RO**. Sprint 8.5 Printer Router idem.
- **Failure mode:** primul tenant restaurant face vânzări reale POS → ANAF cere bonuri fiscale tipărite AMEF certificat → tu ai NoOp → ANAF audit + fine + tenant suspension + reputation reverse.
- **Mitigation:** decide între (a) integrare AMEF real (cost cert ~€500-1500 + printer fiscal ~€300) sau (b) restricționează pilot la restaurant cu **existing AMEF + Z-report import** doar; (c) e-Factura mandatory 2026 — verificat current status SRL → integrare ANAF SPV
- **Owner:** Mihai + contabil consult
- **Decision date:** Înainte de pilot signed

### 🔴 Tigers reclassified din Elephants (Launch-Blocking)

#### T6 (ex-E1). Bandwidth split — cross-context cu Trading Bots T4

- Same observation ca [[2026-05-16 Pre-mortem Trading Bots]] T4. Reservation are deadlines reale (Play Store submit, pilot pursuit) → consequence amplificată.
- **Mitigation:** explicit hour-budget per week per project (vezi T6 [[2026-05-16 Pre-mortem Trading Bots]] mitigation, plus aplicat Reservation)

#### T7 (ex-E2). NOVENTRA SRL runway / funding strategy

- **Evidence:** SRL activ (per memorie SRL Unlock Plan). Obligații lunare: contabilitate (~€100-300/lună), impozit minim, Hetzner (~€50/lună), Stripe fees, Resend, Twilio. Cât capital alocat? Bootstrapped pe salary Stefanini?
- **Failure mode:** 6 luni fără MRR + obligații → drain. Pivot forțat sau dizolvare SRL.
- **Mitigation:** explicit runway calc: cost lunar + buffer 6 luni × salary tradeoff. Decide "cât timp îmi permit fără MRR".
- **Owner:** Mihai
- **Decision date:** 1 săpt

### 🟡 Tigers (Fast-Follow — 2-8 săptămâni)

#### T8. Test coverage asymmetric — `:3000` staff desktop `0 spec` pe Inventory/Recipes/Alerts/Purchasing

- Evidence: `docs/01_CURRENT_STATE.md` tabel explicit
- Mitigation: hot-path E2E coverage sprint dedicat 1 săpt înainte de pilot signed

#### T9. PowerSync local-first scaling necunoscut

- Evidence: Wave 1+2 acceptance pe tenant 1 only. Scale la 2-10? Schema drift? Conflict resolution?
- Mitigation: load test 5 tenants simulat + conflict scenarios

#### T10. CI/CD lipsesc pentru Flutter mobile

- Evidence: Sprint 5 manual build pe laptop personal
- Mitigation: GitHub Actions workflow cu signed AAB (keystore via GitHub Secrets)

#### T11. Subprocessor DPA chain incomplete

- Evidence: `docs/legal/SUBPROCESSORS.md` lists vendors. **DPAs signed pe file?** Verifică Hetzner, Stripe, Resend, Twilio, Firebase
- Mitigation: download standard DPA fiecare vendor + sign + file

### ⚪ Tigers (Track — monitor)

#### T12. Brand "VenueHub" trademark check missing

- Mitigation: EUIPO trademark search 30 min (gratis online) + Romania OSIM dacă necesar

#### T13. SSL Let's Encrypt + domain renewal automation

- Mitigation: confirm cron auto-renewal cert + alert dacă < 30 zile expiry

#### T14. Stefanini employment exclusivity (same elephant ca trading bots T11)

- Mitigation: citește contract muncă + safe HR consult cu general question

### 🐯 Paper Tigers (sound scary, but covered)

- **PT1.** Booking.com / TheFork clone RO — focus EN large markets, nu RO HoReCa nișat
- **PT2.** Postgres won't scale 10k tenants — premature optimization, 0 paying tenants acum
- **PT3.** PowerSync POC fails production — fallback non-local-first mode possible

### 🐘 True Elephants (decision required)

#### E1. **NU există cold outreach plan activ pentru first pilot**

> [!warning] Asta e **Elephant-of-Elephants** pentru Reservation-System.

- **Decision:** prospecting activ acum (cold call/email Romanian restaurants, LinkedIn, hospitality networking) sau accept produsul e side-project și remove "paid pilot" din roadmap.
- **Limbo state = perpetual side project = perpetual zero MRR.**

#### E2. **Feature creep intra-project: spa schelet + Tourist Hub + hotel parallel**

- Evidence: `04_EXECUTION_CONTROL_TOWER.md` Wave 1-11 plan-uite, but Wave 1 (Visual Trust) încă în progres.
- **Decision:** restaurant-only pentru pilot 1 sau toate verticals paralel? **NU vinde all-in-one la primul tenant.**

#### E3. **Mental health / sustainability solo founder pattern**

- Evidence: solo developer, multiple proiecte critical concurrent. Burnout pattern recunoscut industrial (solo SaaS founders cu day job burn out la 12-18 luni dacă fără revenue).
- **Decision:** explicit hour-budget per week per project. Ex: 8h pilot prospecting + 4h tehnic + 2h ibkr_bot diagnostic + 0h crypto-bot + rest = recover.

#### E4. **Mobile app launch (Sprint 5/8A/9/10) — pentru ce paying customer exact?**

- Evidence: pre-mortem reveals dacă T1 (no pilot) e adevărat, app-ul mobil se lansează pentru zero customers concrete. ~40-60h effort.
- **Decision:** continue Play Store launch acum (might attract organic) sau pause până T1 generează pilot care confirmă mobile e wanted.

---

## Phase 6 — Pre-Mortem Summary

```
Total risks identified: 18 + 4 elephants requiring decision
  - Tigers: 14 (Launch-Blocking: 7, Fast-Follow: 4, Track: 3)
  - Paper Tigers: 3
  - True Elephants: 4
```

## Comparat cu Trading Bots pre-mortem

| Aspect | Trading Bots | Reservation-System |
|---|---|---|
| Tigers Launch-Blocking | 4 | **7** |
| Risk type predominant | **Operational** (state, gating, SPOF tehnic) | **Existential** (no paying customer = no business) |
| Mai grav fundamental | Drawdown financiar acut | Erosion + collapse 6-12 luni fără revenue |
| Single biggest insight | Elephants-disguised-as-Tigers (bandwidth, tax) | **Toate eforturile tehnice presupun un destinatar (paying pilot) care nu există încă** |

**Effort ordering corect:** prospect → talk to 3-5 prospects → decide which features gate signature → build those. NU build first hope they come.

## Cross-refs

- Source skill: `~/.claude/skills/borghei-pre-mortem/SKILL.md`
- Sibling pre-mortem: [[2026-05-16 Pre-mortem Trading Bots]]
- Active sprint plan: [[2026-05-06 Client app market readiness sprint plan]]
- Project: [[Reservation-System]]
- Related: [[Strategy resurrection gate]] (for any architectural pivot decision)
