---
title: Projects MOC
type: moc
tags:
  - moc
  - projects
updated: 2026-05-19
---

# Projects MOC

Map of Content pentru toate proiectele active + arhivate. Status preluat din canonical CLAUDE.md per repo.

## 🔴 LIVE (real money)

### [[ibkr_bot]] — `C:\Personal\Trading Bots\ibkr_bot\`
- Equity/futures via IBKR Gateway, server-side automated
- Strategy: **EC_V1**, $385/slot tier
- Server: `207.154.248.232:/opt/ibkr_bot/`
- Telegram bot 8639
- **Risk: HIGH** — diagnostic-first protocol obligatoriu înainte de orice "fix"

## 🟢 ACTIVE — research / paper / tooling (SIM)

### [[Reservation-System]] — `C:\Personal\Reservation-Systems\Reservation-System\`
- SaaS HoReCa multi-tenant (restaurant + hotel + spa + tourist)
- Stack: Spring Boot + React + Flutter
- **Curent:** VPF stack ready through VPF-07; handoff for laptop move: [[Reservation-System - Handoff 2026-05-21 VPF stack to FIT-00]]
- **Next:** `FIT-00 - Fitness Pack Shell` after logical merge order `#94 -> #93`

### [[stocks-bot]] — `C:\Personal\Trading Bots\stocks-bot\`
- SEC Form 4 pipeline → events → enrich → score → ideas → shadow sim
- **PAPER only** — generează semnale EC_V1 consumate de [[ibkr_bot]]
- Active lane: `stocks_event_v2_lane`

### [[alpha-lab]] — `C:\Personal\Trading Bots\alpha-lab\`
- Research laboratory separate de live runtimes
- 4 fronts: ETF/index, SEC events, Crypto carry/basis (research-only), Options Microstructure Overlay shadow MVP
- Promotion: alpha-lab → stocks-bot paper → micro-live → live
- Latest: [[2026-05-19 Options Microstructure Overlay shadow MVP]]

### [[research-orchestrator]] — `C:\Personal\Trading Bots\research-orchestrator\`
- Idea review + debate + voting + outcome tracking
- **A1 digest** producer (deployed 2026-05-08)

## 🟡 TBD status

### [[Pollymarket]] — `C:\Personal\Trading Bots\Pollymarket\`
- `polymarket_mm_v1` — passive MM pe binary objective markets
- Per CLAUDE.md: "Verifică status când atingi"
- `parity_v1` lane în [[stocks-bot]] = frozen (resource isolation issue)

## 🛑 DORMANT (toate strategiile killed)

### [[crypto-bot]] — `C:\Personal\Trading Bots\Crypto_bot\`
- Multi-strategy crypto trading + ML pipeline
- Toate strategiile killed: Lane A, crypto-carry, pullback, breakout
- Service alive, zero semnale
- Telegram bot 8736
- **Strategy resurrection gate** aplicabil — vezi [[Strategy resurrection gate]]

## ⚪ INACTIVE / Marketing

### [[Automation_MVP]] — `C:\Personal\Trading Bots\Automation_MVP\`
- Local orchestrator FastAPI + SQLite + Telegram
- Telegram poller mort din martie 2026
- Status: probably abandoned, needs explicit decision

### [[strategy-os-landing]] — `C:\Personal\Trading Bots\strategy-os-landing\`
- Next.js landing pentru produsul "strategy-os"
- N/A pentru trading pipeline
- Are propriul CLAUDE.md — nu intra peste el

## 🌱 Side / experiments (TBD)

| Folder pe disk | Status |
|---|---|
| `C:\Personal\Dating App` | unclear — TBD revisit |
| `C:\Personal\mini-detect-api` | unclear — TBD revisit |
| `C:\Personal\tools` | utility belt — TBD inventory |
| `C:\Personal\claude-resources` | Claude Code skills sandbox (cloned repos pentru install) |

## Dependency graph (live money pipeline)

```
[[alpha-lab]] (R&D)
    ↓ manual promotion
[[stocks-bot]] (paper, generates EC_V1 signals)
    ↓ signals consumed
[[ibkr_bot]] (LIVE — real money $$$ — diagnostic-first)
    ↑ ↓
[[research-orchestrator]] (A1 digest, cross-venue)
```

**Critical coupling:** schimbări în [[stocks-bot]] `risk_gate` / `ranker` / `lane_registry` pot afecta live trades pe [[ibkr_bot]]. Review cross-context obligatoriu.

---

**Cross-link:** [[Home]] · [[Decisions MOC]] · [[Strategy resurrection gate]]
