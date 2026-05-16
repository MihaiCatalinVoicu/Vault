---
title: Pre-mortem Trading Bots
type: decision
date: 2026-05-16
status: executed
impact: strategic
horizon: 14-weeks
tags:
  - decision/risk-analysis
  - decision/strategic
  - product/trading
---

# 2026-05-16 — Pre-mortem Trading Bots pipeline

Aplicat skill-ul `borghei-pre-mortem` pe scope-ul `C:\Personal\Trading Bots\`. Cadrul Klein 2007 (prospective hindsight) + clasificare Tigers/Paper Tigers/Elephants. Horizon 14 săptămâni (orizont realist pentru regime shift + drawdown structural).

## Scope

Întreaga umbrelă trading bots:
- Live money: [[ibkr_bot]] (EC_V1, $385/slot, server `207.154.248.232`)
- Signal generator: [[stocks-bot]] (PAPER, feeds EC_V1 → ibkr_bot)
- Research: [[alpha-lab]] (R&D, 3 fronts) + [[research-orchestrator]] (A1 digest)
- Dormant: [[crypto-bot]] (4 strategies killed, service still running)
- TBD: [[Pollymarket]] (`parity_v1` lane frozen)
- Inactive: [[Automation_MVP]] (Telegram poller dead March 2026)
- Marketing: [[strategy-os-landing]] (Next.js)

## Phase 1 — Failure framing

> *"E 14 săptămâni de azi (sfârșit August 2026). Trading Bots pipeline a eșuat. Una sau mai multe: cash drawdown semnificativ pe ibkr_bot, IBKR account flagged, stocks-bot a pus semnale greșite în production, alpha-lab a generat noise în loc de edge, crypto-bot uitat consumă cost fără valoare, Pollymarket încă TBD persistent, ANAF a întrebat ceva incomod. Ce a mers prost?"*

## Phase 2-5 — Risk Registry

### 🔴 Tigers (Launch-Blocking — must mitigate NOW)

#### T1. IBKR Gateway 2FA = single point of failure pe phone

- **Evidence:** Friction event 2026-05-08 (698+ crash-loop restarts). Alert 2026-05-15 (Gateway down >5min waiting push). Vezi [[2026-05-15 IBKR 2FA reconnect protocol]].
- **Failure mode:** Vacanță fără cover, telefon dead 4h, spital, avion, sleep heavy → Gateway needs reconnect → push expiră → bot offline 12-24h+ → poziții deschise neînchise la timing corect → drawdown necontrolat.
- **Mitigation:**
  - (a) Enable IBKR "Multiple concurrent sessions" în Client Portal → settings → User Settings
  - (b) Backup mobile device cu IBKR Mobile + creds în vault personal
  - (c) Watchdog setup: push email/SMS dacă Gateway down >5min (în plus față de Telegram)
- **Owner:** Mihai
- **Decision date:** Înainte de orice vacation >24h sau weekend without phone

#### T2. stocks-bot SIM → ibkr_bot LIVE coupling fără gating layer

- **Evidence:** `Trading Bots/CLAUDE.md` explicit: "stocks-bot generează semnale citite de ibkr_bot". stocks-bot PAPER, ibkr_bot LIVE.
- **Failure mode:** Refactor pe scoring/ranker/risk_gate, un signal score umflat → ibkr_bot trade pe semnal corrupted. Sau race condition pe scoring care reproduce zile la rând fără observation. Single bad signal = material loss pe live money.
- **Mitigation:**
  - (a) Paper-shadow window obligatoriu min 7 zile pe orice change pe scoring/ranker/risk_gate
  - (b) Drift detection: alert dacă signal score schimbă >X% vs istoric pe același input
  - (c) Manual approval pentru orice merge pe `main` care atinge `src/risk/`, `src/scoring/`, `configs/lane_registry.yaml` în [[stocks-bot]]
- **Owner:** Mihai
- **Decision date:** Înainte de următoarea deploy stocks-bot

#### T3. state.json corruption pe ibkr_bot

- **Evidence:** CLAUDE.md flag state.json ca core diagnostic. Friction event 2026-05-08 sugerează state involvement în crash-loop.
- **Failure mode:** Concurrent write mid-cycle, crash mid-write → state corrupted → bot la repornire pierde tracking → re-open closed positions sau over-sizing → live capital loss. Sau silent disagree state local vs IBKR portofoliu real.
- **Mitigation:**
  - (a) Atomic writes: `.state.json.tmp` + fsync + rename
  - (b) Checksum la write + verify la read
  - (c) Boot reconciliation: fetch IBKR portfolio și diff vs state.json — flag dacă mismatch
  - (d) Versioned backup state.json la fiecare 1h (păstrează 24h)
- **Owner:** Mihai
- **Decision date:** Înainte de orice schemă change pe state

### 🔴 Tigers reclassified din Elephants (Launch-Blocking)

#### T4 (ex-E1). Bandwidth split: 4+ projects active, 1 human

- **Evidence:** Reservation-System (10-sprint market readiness), ibkr_bot live ops, stocks-bot dev, alpha-lab R&D, vault setup, crypto-bot dormant decisions, IBKR ops, Stefanini day job. Realistic — care e probabilitatea că în 2 săptămâni atingi 3+ cu adevarat?
- **Why disguised:** Bandwidth thinning = silent failures în zone nemonitorizate. ibkr_bot LIVE money e zona unde silent failure costă mult, dar e zona pe care o atingi cel mai puțin ("set and forget" până nu mai e).
- **Mitigation:**
  - Prioritization explicită: care 2 proiecte primesc focus în următoarele 4 săptămâni?
  - Restul = parked cu state documented (sau în vault, sau în CLAUDE.md per repo)
  - Weekly review: 1 oră fix-time pentru ibkr_bot diagnostic (`journalctl`, state.json sanity, IBKR cross-check) — chiar dacă nu pare "nevoie"
- **Owner:** Mihai (decision facilitation cu Claude OK)
- **Decision date:** ASAP — această săptămână

### 🟡 Tigers (Fast-Follow — 2-4 săptămâni)

#### T5 (ex-E2). ANAF tax exposure pe trading personal

- **Evidence:** Bot face trades automate pe IBKR (Romania resident). Câștiguri de capital → tax declaration anuală. Niciun system pentru extract trades pentru tax season menționat în niciun proiect.
- **Failure mode:** Tax surprise în aprilie 2027 (10-16% pe profituri + penalty întârziere). Sau worse: ANAF audit cu retroactive penalties dacă declarații istoric incomplete.
- **Mitigation:**
  - Consultant fiscal 1h consultanță: ce documente, ce form, ce frecvență
  - Tooling: script care extrage IBKR trades în format ANAF-compatibil (lunar/anual)
  - Decide tax structure: declarat ca PFA? Sub NOVENTRA SRL ca activitate comercial? Sau personal câștig de capital?
- **Owner:** Mihai + consultant fiscal extern
- **Decision date:** Înainte de Q1 2027 close (martie)

#### T6 (ex-T4 original). Pollymarket parity_v1 lane "frozen until runtime mapping fixed"

- **Evidence:** stocks-bot/configs/lane_registry.yaml explicit. Vezi [[Pollymarket]].
- **Failure mode:** Re-activare fără rezolvare runtime isolation → conflict pe shared host → alte bots crash în lanț.
- **Mitigation:** NU re-activa până nu există resource isolation documentat. Decide A/B la T8 (acoperit jos).

#### T7 (ex-T5 original). alpha-lab "crypto carry / basis" research front colide cu [[2026-05-08 Crypto-carry abandoned]]

- **Evidence:** alpha-lab README listează "Crypto carry / basis" ca research front activ. Dar killed decision: structural dead per BIS+BitMEX 2025.
- **Failure mode:** Research re-derivează aceeași hypothesis dead, generează backtests false-positive, promotion path o aduce înapoi în stocks-bot → ibkr_bot. Loop closed pe mecanism deja killed.
- **Mitigation:**
  - Update alpha-lab README: explicit constraint "crypto-carry research must confront BIS+BitMEX 2025 findings before any promotion"
  - Add la [[Strategy resurrection gate]] reference în CLAUDE.md alpha-lab
  - Promotion path gate explicit: backtest in-sample insufficient, must show out-of-sample post-2024 viability

### 🟢 Tigers (Track — monitor, react if escalates)

#### T8 (ex-T6 original). Stefanini laptop = de facto dev env pentru live money repos

- **Mitigation:** Migrate live ops (deploy commands, state inspection) la laptop personal. Parte din strategy split [[2026-05-16 Setup Obsidian vault]].

#### T9 (ex-T7 original). A1 digest regression dependency în ibkr_bot decision context

- **Mitigation:** Version pin + digest freshness check + bot ignore digest dacă > N ore vechi.

#### T10 (ex-E3). Single-developer monoculture, no transfer plan

- **Mitigation:** Documentează "kill switch" remote (cum oprești bot fără laptop/access). Brief 1 trusted person (frate? partener?) cu instrucțiuni minimal "cum să stop bot în urgență".

#### T11 (ex-E6). Stefanini employment exclusivity / conflict of interest

- **Mitigation:** Citește contract muncă Stefanini secțiunea "side activities" / "exclusivity" / "moonlighting". Dacă ambiguity → consultă HR safe (general question, nu disclose specific).

### 🐯 Paper Tigers (sound scary, but unlikely/managed)

- **PT1.** Strategiile crypto-bot pot rezuscita organic — covered prin [[Strategy resurrection gate]]
- **PT2.** Vault GitHub leakează knowledge sensitiv — repo private, no secrets, backup via clone
- **PT3.** GitHub disable repos pentru TOS — open source code, low probability

### 🐘 True Elephants (decision required, NU Tiger)

#### E4. Pollymarket "TBD" persistent zombie

- **Cognitive load:** small, but accumulates.
- **Decision:** ACTIVE cu plan concret (1 sprint week dedicat) sau ARCHIVED explicit (move la `archive/` + decision file). **NU TBD persistent.**

#### E5. crypto-bot DORMANT cu serviciul still running

- **Decision:** Kill serviciul (free resources) sau Justify de ce rulează (e.g. monitoring reuse) — cu date pe cost lunar.

## Phase 6 — Verdict scurt

| Categorie | Count | Action |
|---|---|---|
| 🔴 Launch-Blocking (incl. ex-Elephants) | **4** | Mitigation NOW |
| 🟡 Fast-Follow | **3** | Mitigation 2-4 săpt |
| ⚪ Track | **4** | Monitor + react |
| 🟢 Paper Tigers | 3 | No action — documented |
| 🐘 True Elephants (decision) | 2 | Force decision: ACTIVE or ARCHIVED |

**Observație cu greutate maximă:** Elephants-disguised-as-Tigers (T4 bandwidth, T5 tax, T10 single-dev) sunt cele care ucid pipelines reale. Mai mult decât T1/T2/T3 tehnic (fix-uri cu work known).

## Cross-refs

- Source skill: `~/.claude/skills/borghei-pre-mortem/SKILL.md` (cherry-picked via [[2026-05-16 Pre-mortem skill cherry-pick]])
- Standing gate: [[Strategy resurrection gate]]
- Related projects: [[ibkr_bot]] · [[stocks-bot]] · [[alpha-lab]] · [[crypto-bot]] · [[Pollymarket]] · [[research-orchestrator]]
- Process protocol: [[2026-05-15 IBKR 2FA reconnect protocol]]
- Past killed strategies: [[2026-05-10 Lane A funding-contrarian killed]] · [[2026-05-08 Crypto-carry abandoned]]
