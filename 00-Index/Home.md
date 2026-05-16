---
title: Home
type: index
tags:
  - moc
  - home
updated: 2026-05-16
---

# Home — VenueHub Vault

> [!info] Pentru Claude
> Acesta este punctul de intrare. La fiecare sesiune nouă, citește această pagină primul ca să afli starea curentă. De aici naviga prin wikilinks la proiectele active + deciziile recente.

## Active projects (status azi)

### 🔴 LIVE — real money

| Proiect | Status | Pointer |
|---|---|---|
| [[ibkr_bot]] | 🔴 LIVE — EC_V1 strategy, $385/slot tier | Server `207.154.248.232`. Diagnostic-first protocol obligatoriu. |

### 🟢 Active — research / paper / tooling

| Proiect | Status | Pointer |
|---|---|---|
| [[Reservation-System]] | client_app market readiness in execution | Sprint 5/8A/9/10 pending pe laptop personal |
| [[stocks-bot]] | PAPER → genereaza semnale EC_V1 pentru ibkr_bot | Active lane: `stocks_event_v2_lane` |
| [[alpha-lab]] | R&D research lab, 3 fronts active | NU live order routing |
| [[research-orchestrator]] | TOOLING — A1 digest deployed 2026-05-08 | Multi-venue digest pentru ibkr_bot context |

### 🟡 TBD / 🛑 Dormant / ⚪ Inactive

| Proiect | Status |
|---|---|
| [[Pollymarket]] | 🟡 TBD — verifică status când atingi |
| [[crypto-bot]] | 🛑 DORMANT — toate strategiile killed. Vezi [[Strategy resurrection gate]] |
| [[Automation_MVP]] | ⚪ Telegram poller mort din martie 2026 |
| [[strategy-os-landing]] | ⚪ Next.js landing, are propriul CLAUDE.md |

## Recent decisions (newest first)

- [[2026-05-16 Pre-mortem Trading Bots]] — **16 risks, 4 Launch-Blocking**: IBKR 2FA SPOF, stocks→ibkr coupling, state.json corruption, bandwidth split
- [[2026-05-16 Pre-mortem skill cherry-pick]] — installed borghei-pre-mortem (audited safe)
- [[2026-05-16 Setup Obsidian vault]] — initialized this vault
- [[2026-05-15 IBKR 2FA reconnect protocol]] — must tap notification, NOT login in-app
- [[2026-05-10 Lane A funding-contrarian killed]] — Sharpe -1.14 over 18mo backtest
- [[2026-05-08 Crypto-carry abandoned]] — structural dead per BIS+BitMEX 2025 research
- [[2026-05-06 Client app market readiness sprint plan]] — 10-step plan for Play Store Internal Testing

## Reference (re-read often)

- [[Stefanini laptop monitoring]] — what's logged + what's safe to do
- [[Claude session boundary]] — rules of engagement for Claude in this vault
- [[Git workflow]] — vault sync cross-laptop
- [[Strategy resurrection gate]] — NU re-activa strategies killed fără gate strict
- [[Telegram bots map]] — bot_id per serviciu

## How to use this vault

1. **Picking up after time away?** Read this Home, scan [[Active projects]] + [[Recent decisions]].
2. **New idea / quick capture?** Drop in [[90-Inbox]] folder, sort later.
3. **Made a decision?** Create file in `20-Decisions/` with template `[[_templates/Decision]]`.
4. **End of day reflection?** `30-Daily/YYYY-MM-DD.md` with template `[[_templates/Daily]]`.

## Maps of Content (MOC)

- [[Projects MOC]] — full project map
- [[Decisions MOC]] — chronological + by-topic decision log

---

**Vault setup:** 2026-05-16 on Stefanini laptop, will move to personal laptop primary.
**Cross-laptop sync:** via git (vezi [[Git workflow]]).
