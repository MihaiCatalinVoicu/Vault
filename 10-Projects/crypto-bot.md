---
title: crypto-bot
type: project
status: dormant
stack:
  - Python
  - Hyperliquid + (others)
tags:
  - project/dormant
  - product/trading
  - product/automated
created: 2026
updated: 2026-05-16
---

# crypto-bot

Multi-strategy crypto trading bot. Status azi: **DORMANT** — toate strategiile killed, serviciul rulează dar nu generează semnale. Necesită R&D pentru o strategie nouă; nu e blocking.

## On-disk

`C:\Personal\Trading Bots\` (umbrella) — subfolder TBD.

## Telegram bot

bot_id = **8736** (post swap 2026-05-08) — vezi [[Telegram bots map]].

## Strategy history (toate killed)

| Strategy | Status | Why killed | Date |
|---|---|---|---|
| **crypto-carry** | 🔴 ABANDONED | structural dead per BIS + BitMEX 2025 research | 2026-05-08 — vezi [[2026-05-08 Crypto-carry abandoned]] |
| **pullback** | 🔴 KILLED | Sharpe -4.31 backtest | earlier 2026 |
| **breakout** | 🔴 KILLED | sub-sample, unreliable | earlier 2026 |
| **Lane A funding-contrarian** | 🔴 KILLED | Sharpe -1.14 over 18mo backtest | 2026-05-10 — vezi [[2026-05-10 Lane A funding-contrarian killed]] |

## Current state

- Service alive (process running, no crash)
- Zero semnale (toate timers/strategies disabled)
- Cod inert pentru strategies killed — păstrat ca referință arhivă, NU re-activa fără re-research

## Hyperliquid + HLP onboarding

User gestionează onboarding cu ghidul la `Trading Bots/docs/hyperliquid-onboarding.md`. $50-100 capital planned.

## Next steps (TBD, fără urgență)

- Decide R&D direction (momentum reversal? cross-exchange arbitrage? funding rate redesign cu hedge?)
- Re-examine market structure assumptions (au schimbat exchange-urile rules 2025+?)
- Set sizing prudent dacă re-pornește

## Cross-refs

- Killed strategies: [[2026-05-10 Lane A funding-contrarian killed]] · [[2026-05-08 Crypto-carry abandoned]]
- Related: [[ibkr_bot]] (separate, equity/futures via IBKR)
