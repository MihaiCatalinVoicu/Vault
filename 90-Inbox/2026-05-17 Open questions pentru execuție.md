---
title: Open questions pentru execuție mâine
type: prep
date: 2026-05-16
target-date: 2026-05-17+
status: pending-answers
tags:
  - prep
  - execution
  - questions
---

# Mâine — întrebări deschise pentru execuție

> Scris la sfârșitul zilei 2026-05-16 (post 12h muncă). Citește la prima sesiune mâine. Răspunde la întrebările concrete jos pentru fiecare punct — Claude pornește execuția cu input clar.

## Poziția confirmată

- **Front principal:** [[Reservation-System]] (HoReCa SaaS)
- **Side hustle:** [[Trading Bots]] — defensive maintenance only, NU strategic effort
- **NU acceptăm:** "produsul nu merge" ca outcome. Mitigation fierce, NU pivot.
- **Tot e load-bearing:** Accounting Ops + Full External Reservations + Fast Ops integral toate 3 verticals (restaurant + hotel + spa)
- **Expansion paralelă planificată:** Medical equipment accounting line (engine reuse + OCR + fiscal layer)

---

## Punct 1 — Trading Bots T1 (IBKR multi-session enable)

**Ce știu:** 5 min work. Click-uri pe Client Portal. Vezi [[2026-05-15 IBKR 2FA reconnect protocol]].

**Întrebări pentru tine:**

- Q1.1 — Vrei să facem asta înainte de Reservation-System mâine? E quick win independent.

**Effort mâine:** 5 min (tu pe Client Portal).

---

## Punct 2 — Reservation T3 (Postgres backup automation)

**Ce știu:**
- Hetzner `89.167.121.99`
- Postgres în docker-compose (`container_name: postgres`, port 55432 → 5432)
- DB `reservationsystem`, user `admin`, password `admin` (per `scripts/run-backend-local.ps1`)
- PowerSync logical replication = NU e backup, doar replication

**Întrebări pentru tine:**

- Q2.1 — Cloud storage preferat: **Hetzner Storage Box** (€3.20/lună, same DC, low latency — recomandare), Backblaze B2 (€0.005/GB/lună, EU region), sau AWS S3 (mai scump)?
- Q2.2 — Ai deja cron-uri sau task scheduler pe Hetzner sau e clean slate? (verifică `crontab -l` și `ls /etc/cron.*`)
- Q2.3 — Există deja un `pg_dump` manual rulat vreodată? Backup-uri istorice undeva?
- Q2.4 — Retention period: 7 zile, 30 zile, 90 zile?

**Effort mâine:** 30-45 min total. Eu scriu script-ul de backup + rclone config + cron entry. Tu copy-paste pe Hetzner + run setup-ul.

**Output:** backup zilnic automated + 1 restore drill manual confirmed.

---

## Punct 3 — Reservation T13 (SSL Let's Encrypt auto-renewal)

**Ce știu:**
- `infra/nginx-host/venuehubhq.com.conf` referă `/etc/letsencrypt/live/venuehubhq.com/fullchain.pem`
- Standard certbot setup pe Hetzner host

**Întrebări pentru tine:**

- Q3.1 — SSH access pe Hetzner — folosești terminal Windows direct, WSL, sau Git Bash? (relevant pentru cum îți dau comenzile)
- Q3.2 — User SSH e `root` sau alt user cu sudo?

**Effort mâine:** 5 min (tu pe Hetzner cu `certbot renew --dry-run` + verifică crontab).

**Output:** confirm cert renewal merge fără probleme + alert dacă <30 zile expiry.

---

## Punct 4 — Reservation T12 (Trademark "VenueHub")

**Ce știu:** EUIPO + OSIM (RO) search gratis online. 30 min manual.

**Întrebări pentru tine:**

- Q4.1 — Vrei să faci search-ul cap-coadă mâine sau preferi să-l împart în 2: (a) acum search rapid EUIPO de tine, (b) decizie pe baza rezultat?
- Q4.2 — Ai în mind alternative branding ready dacă "VenueHub" e taken? (gen "VenueOps", "HoReCaHub", etc.) — important să ai măcar 2-3 backup names înainte de search

**Effort mâine:** 30 min (tu pe euipo.europa.eu/eSearch + osim.ro). Eu interpret rezultate dacă-mi spui ce găsești.

---

## Punct 5 — NOVENTRA SRL runway calc

**Ce știu:**
- NOVENTRA SYSTEMS S.R.L. activ (per memorie + Privacy Policy)
- CUI 54594735, J2026028611002
- Cost-uri evident: Hetzner, contabilitate firmă, domain renewal anual
- Cost-uri condiționate: Stripe fees (volume-based), Resend ($10+ if activated), Twilio ($10+ if activated)

**Întrebări pentru tine (sensibilitate financiară — răspunde doar cu ce ești comfortable să împărtășești):**

- Q5.1 — Cost lunar fix contabilitate firmă: aproximativ €/lună?
- Q5.2 — Hetzner: ce abonament/server? (ex: CCX13 ~€15, CPX21 ~€8, dedicated AX series? — verifică pe Hetzner Cloud sau Robot)
- Q5.3 — Domain `venuehubhq.com` annual renewal cost (aprox)?
- Q5.4 — TVA: SRL e plătitor TVA sau sub plafon (€88,500 echivalent)?
- Q5.5 — Servicii recurente plătite deja: GitHub Pro? Cursor? Anthropic API credits? (relevant pentru cost-tracking)
- Q5.6 — Buffer financiar disponibil pentru SaaS dezvoltare (lichid, fără să atingi rate / obligații): 6 luni / 12 luni / >12 luni? (NU îmi cere suma, doar orizont)
- Q5.7 — Stefanini job: alocarea de ore SaaS afectează performance Stefanini, sau poți menține 40h Stefanini + ore SaaS separate?

**Effort mâine:** 1-2h (tu cu Excel sau hârtie). Eu pot să fac un template Excel mai târziu dacă vrei.

**Output:** decision file nou în vault `2026-05-17 NOVENTRA SRL runway baseline.md` cu cifră concretă "rămân X luni la rate-ul curent".

---

## Punct 6 — Demo video 90s (cel mai mare ROI strategic)

**Ce știu:** Tot setup-ul e în [[Demo video strategy]]. Playwright + OBS + ElevenLabs. Scenariu 6-scene pre-scris.

**Întrebări pentru tine:**

- Q6.1 — Tools already installed?
  - **OBS Studio**: Da/Nu (gratis, obsproject.com)
  - **Playwright** în repo: există în `package.json` cu `e2e/` dir — confirmat installed
  - **ElevenLabs cont**: Ai cont? Free tier 10k caractere/lună suficient pentru 90s × 2 limbi
  - **Loom** sau alt host video: Ai cont? (Loom free 5 min limit, OK pentru 90s)
- Q6.2 — Demo tenant cu date realiste: există un tenant seed-uit cu nume Romanian-realistic (NU "Test 1", ci "Restaurant La Mama") sau trebuie construit?
- Q6.3 — Voice preferință: RO doar (target HoReCa RO), EN doar (investor pitch), sau ambele?
- Q6.4 — Timing: 1 zi focused (~8h) sau 2 zile spread? Recomand 1 zi focused — context switching e killer pe video editing.

**Effort mâine sau weekend:** 8-12h total. Pot să te ajut cu:
- Scrie Playwright spec complete pe baza CLAUDE.md + features actuale
- Seed scripts pentru date realiste
- Naration script final RO + EN

**Tu faci:**
- Recording OBS pe device personal (NU Stefanini — leak risk + cursor demos arătate)
- Voice generation ElevenLabs
- Editing final + upload Loom

---

## Punct 7 — External Reservations 3 PR MVP

**Ce știu:**
- EXT-RES-001 sprint închis 2026-05-09 (foundation done)
- 9 controllers wired
- Email ingestion + bulk import funcționale
- Plan 15 PR-uri în `docs/strategy/RESERVATION_INTAKE_COMMAND_CENTER.md`

**Întrebări pentru tine:**

- Q7.1 — Selecția mea recomandată pentru pilot MVP:
  - **RES-CORE-001** — core mechanics solid
  - **EXT-RULES-001** — auto-rules engine declarative (CRUCIAL pentru "wow factor" demo)
  - **EXT-CONFLICT-001** — conflict resolution UI (CRUCIAL pentru claim "absoarbe haosul")
  
  Sunt astea 3 OK sau ai input diferit? Vezi tu doc strategic 15-PR și-mi spui dacă alta combinație 3-PR are sens.
  
- Q7.2 — Timeline: 2 săpt focused (= bandwidth tradeoff vs Pasul 4 outreach) sau spread 4 săpt în paralel cu outreach?
- Q7.3 — Vrei să fac eu read-through pe strategic doc ca să propun MVP alternativă mai informată? (1-2h analysis în repo, decision file nou)

**Effort mâine:** 1h decision + planning. Build-ul propriu-zis = 2-4 săpt depending pe selecție.

---

## Punct 8 — Fast Ops hotel + spa UI parity

**Ce știu:**
- 8 active feature branches: `fastops-1to1-ux-functional-parity`, `fastops-backend-api-clean`, `fastops-dev-seed-clean`, `fastops-frontend-parity-clean`, `fastops-functional-parity-clean`, `backup/fastops-local-wip`, `safety/fastops-local-wip-20260514-141429`, `rescue/ext-bulk-001-local`
- Backend services pentru toate 3 verticals există (Restaurant 41 LOC, Hotel 60 LOC, Spa 60 LOC)
- Recent commit `3758b12f` adăugat walk-in/move-room/maintenance-window/spa-walk-in endpoints

**Întrebări pentru tine:**

- Q8.1 — Care branch e "main work-in-flight" pentru hotel/spa parity? Există un ordering clar sau toate sunt experimentale?
- Q8.2 — Estimează cât timp până la "fast ops integral pe toate 3 verticals": 1 săpt? 2 săpt? Lună?
- Q8.3 — În pilot 1 (restaurant primarily) — hotel + spa fast-ops vor fi visible la onboarding tenant restaurant? Sau hidden complet?

**Effort:** depinde de answer Q8.1 — pot să fac eu read-through pe branches majore pentru a vedea status concret.

---

## Punct 9 — Operational hardening pre-pilot (T2 + T4 + T5)

Astea sunt **dependent pe Pasul 5 din planul anterior** = răspuns pozitiv din outreach. Le notez pentru pregătire:

### T2 — Hetzner SPOF mitigation

- Q9.1 — Cold standby preferință: alt DC Hetzner (mai sigur, ~2x cost) sau alt server în același DC (ieftin, less DR)?
- Q9.2 — Există deja un Hetzner snapshot la nivel de host (Hetzner Cloud daily snapshot)?

### T4 — GDPR rehearsal

- Q9.3 — Avocat GDPR în RO — ai contact sau e zero-start? Cost consult 1h aprox €100-300.
- Q9.4 — DSAR mock cu user real — ești OK să folosim un user demo + tu acționezi ca "data subject requesting export" pentru rehearsal?

### T5 — Fiscal RO (AMEF)

- Q9.5 — Cunoști pe cineva care a făcut integrare AMEF certificat în RO? Sau full discovery from scratch?
- Q9.6 — Decizie strategy:
  - **A:** Investiție AMEF cert hardware now (~€800-1800) pentru a putea vinde la restaurante fără infrastructure existentă
  - **B:** Restrict pilot 1 la restaurant cu existing AMEF + import Z-report (mai îngust target, mai rapid go-live)
  
  Care preferi pentru pilot 1?

**Effort:** TBD până la confirmation prospect (Pasul 4 outreach).

---

## Punct 10 — Outreach prima salvă (post Demo video)

**Ce știu:** plan în [[Demo video strategy]] (mesaje template + checklist).

**Întrebări pentru tine:**

- Q10.1 — LinkedIn premium sau standard? (Premium = 30 InMail/lună, mult mai bun pentru cold outreach decât DM)
- Q10.2 — Network existent HoReCa: zero connections, câteva connections, network solid?
- Q10.3 — Geografie focus: București doar / multi-city (Cluj, Iași, Timișoara, Brașov) / national?
- Q10.4 — Email outreach: din `contact@venuehubhq.com` (need MX records + email setup) sau din personal Gmail/Yahoo?
- Q10.5 — Volume realist 5/zi sau 10/zi? Sustainable rate matters more than burst.

**Effort mâine:** 2-3h (build target list 30 contacte + customize messaging). 30 min/zi outreach ongoing.

---

## Punct 11 — Medical equipment accounting line (EXPANSION SEPARATĂ)

**Ce știu:** Tu ai descris: engine accounting existing + ingest facturi PDF/PNG/JPEG (OCR) + full fiscal layer peste. Niche complet diferită de HoReCa.

**Întrebări pentru tine (când suntem gata să discutăm — NU mâine, doar prep):**

- Q11.1 — Ai un lead/client identificat pe medical (1 distributor care a zis "vreau") sau e idea ta din observare piață?
- Q11.2 — OCR tehnologie preferință: open-source (Tesseract, ~free, decent quality), comerc (Azure Form Recognizer ~$1/1000 docs, very good), Claude Vision API (cost-effective dacă deja ești pe Anthropic)?
- Q11.3 — Tu vezi medical ca: (a) spin-off product separate cu propriul brand, (b) module add-on în VenueHub, (c) cross-vertical produs unified?
- Q11.4 — Timeline aprox: paralel cu HoReCa pilot din ziua 1, sau după ce HoReCa are pilot signed?

**NOT pentru mâine.** Notez ca decision file dedicat: `2026-05-XX Medical accounting expansion plan.md` când îmi spui să atac.

---

## Sumar pentru mâine — ordine sugerată

1. **Citire matinală vault:** [[Home]] + acest fișier
2. **Trecere prin întrebări:** răspunde la Q-uri (focused, 30-60 min)
3. **Atac pe ordine:**
   - Quick wins: Q1.1 IBKR (5 min) → Q3.1+Q3.2 SSL (5 min) → Q4.1+Q4.2 Trademark (30 min)
   - Apoi Q2.1-Q2.4 Postgres backup (45 min)
   - Apoi Q5.1-Q5.7 NOVENTRA runway (1-2h)
   - **Pauză / lunch**
   - Apoi Q6.x Demo video planning (1-2h)
   - Apoi Q10.x Outreach planning (1h, decisions only)

Total mâine: 6-8h dacă vrei să termini quick wins + runway + video planning. Sau spread peste 2 zile dacă bandwidth limitat.

## Stop conditions mâine

- Dacă oboseala lovește la 4h → oprește, NU forța. Quick wins (1-1.5h) + runway calc (1h) e suficient achievement pentru o zi.
- Demo video building (8-12h total) merită propria zi separată — NU îl combina cu altele.

## Pentru azi (final)

- Salvează acest fișier în memorie ca "primul lucru de citit mâine"
- Mănâncă, hidratează, dormi
- NU lucra mai mult

## Cross-refs

- [[2026-05-16 Reservation-System pre-pilot readiness]]
- [[2026-05-16 Pre-mortem Reservation-System]]
- [[2026-05-16 Pre-mortem Trading Bots]]
- [[Demo video strategy]]
- [[Home]]
