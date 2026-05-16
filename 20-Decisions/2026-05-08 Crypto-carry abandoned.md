---
title: Crypto-carry abandoned
type: decision
date: 2026-05-08
status: executed
impact: strategy
tags:
  - decision/strategy-kill
  - product/crypto-bot
---

# 2026-05-08 — Crypto-carry abandoned

## Context

Crypto-carry = strategie în [[crypto-bot]] bazată pe basis trade (long spot + short perp, sau invers) pentru a captura funding rate. Premisa istorică: perpetual futures pe crypto au funding rate predictabil pozitiv → long basis trade = yield ~10-30% annualized.

## Verdict

🔴 **ABANDONED 2026-05-08.** Timer disabled. Cod marcat inert. **NU re-activa.**

## Evidence

**Research extern (BIS + BitMEX 2025):**
- Funding rates au comprimat dramatic 2024-2025 (median funding < 0.01% per 8h în multe pair-uri majore)
- Liquid spot ETFs (BTC, ETH) au shifted hedging flows din retail perp către instituționali, comprimând basis
- Cost de execution (slippage + fees) > yield așteptat pe trade size mic-mediu

## Why it was structurally dead

Crypto-carry funcționează când există dislocation persistentă între spot și perp funding rate. Asta s-a întâmplat 2019-2022 datorită:
1. Retail-heavy speculation pe perps cu leverage mare → funding rate constant pozitiv
2. Lipsa de instituționali să arbitrațeze rapid
3. Spot ETFs nu existau → limited natural shorts

2024-2025 a schimbat fiecare din ele:
1. Retail share scăzută (post 2022 crash)
2. Instituționali profesioniști arbitrațează minute-by-minute
3. BTC/ETH spot ETFs → enormus liquid hedging flows comprimă basis

→ Strategy nu mai are edge structural. NU e bug în implementation; e schimbare de regim pe piață.

## Standing rule

**NU re-activa crypto-carry** oricât ar arăta tempting în backtest forward. Re-research necesar înainte de orice re-pornire — și research-ul trebuie să dovedească reverse pe BIS + BitMEX 2025 findings.

## Implications

- Timer disabled pe service-ul [[crypto-bot]]
- Cod inert păstrat ca arhivă, nu activated

## Pattern

Aceeași observație ca în [[2026-05-10 Lane A funding-contrarian killed]]: strategiile bazate pe market structure pre-2024 nu mai funcționează. Pattern comun = mean-reversion sau funding-arbitrage devenit ineficient cu efficient instituționali în market.

## Cross-refs

- [[crypto-bot]] — proiect părinte
- [[2026-05-10 Lane A funding-contrarian killed]] — pattern related kill
- Hyperliquid + HLP onboarding ghid: `Trading Bots/docs/hyperliquid-onboarding.md` (alt approach)
