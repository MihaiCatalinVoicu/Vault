---
title: Lane A funding-contrarian killed
type: decision
date: 2026-05-10
status: executed
impact: strategy
tags:
  - decision/strategy-kill
  - product/crypto-bot
---

# 2026-05-10 — Lane A funding-contrarian killed

## Context

Lane A = strategy în [[crypto-bot]] bazată pe funding-contrarian logic (long când funding rate e negativ, short când e pozitiv excesiv — bet pe mean-reversion al funding-ului). Rulat live pe perioadă scurtă, plus backtest istoric extensiv.

## Verdict

🔴 **KILLED 2026-05-10.** Cod inert. NU re-activa.

## Evidence

**Backtest 18 luni:**
- Sharpe = **-1.14**
- Pierdere consistentă, nu noise pe perioadă scurtă

## Why it failed (rationale onestă)

[TBD — citeste cărui hypothesis a fost rezolvat de market: funding rates s-au comprimat? lichiditate? leverage cycle? Adaugă aici post-mortem detaliat.]

## Pattern comun cu alte strategii killed

Toate 3 strategii killed în [[crypto-bot]] au în comun:
- **Funding/perpetual mechanics** — pullback (Sharpe -4.31), breakout (sub-sample), Lane A (-1.14)
- **Backtest în-sample arăta promițător, out-of-sample / live a confirmat deteriorare**

Asta sugerează ipoteza: **structura pieței crypto-perp a evoluat 2024-2026** într-o direcție care face mean-reversion strategies harder (more efficient pricing, less retail-driven dislocation). Vezi [[2026-05-08 Crypto-carry abandoned]] pentru pattern similar.

## Implications

- [[crypto-bot]] = DORMANT acum (toate strategiile killed)
- Service alive dar zero semnale
- Înainte de a re-porni cu strategie nouă: examine market structure changes 2025+, NU repeat aceeași hypothesis

## Standing rule

**NU re-activa Lane A** oricât ar arăta tempting pe forward backtests. Cod marcat inert ca paper trail; deactive timer + signal handler.

## Cross-refs

- [[crypto-bot]] — proiect părinte
- [[2026-05-08 Crypto-carry abandoned]] — pattern similar
- [[Decisions MOC]]
