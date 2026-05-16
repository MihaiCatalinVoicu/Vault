---
title: Strategy resurrection gate
type: reference
tags:
  - reference/process
  - reference/trading
  - reference/gate
updated: 2026-05-16
---

# Strategy resurrection gate

Regulă care derivă din `C:\Personal\Trading Bots\CLAUDE.md`. Aplicabil pentru orice strategy aflată în memorie sau în vault ca **killed / dormant / abandoned**.

> [!warning] NU re-activa o strategy killed fără TOATE condițiile bifate.

## Strategies în starea "killed/dormant/abandoned"

| Strategy | Project | Status | Killed | Detail |
|---|---|---|---|---|
| Lane A funding-contrarian | [[crypto-bot]] | 🔴 killed | 2026-05-10 | [[2026-05-10 Lane A funding-contrarian killed]] |
| crypto-carry | [[crypto-bot]] | 🔴 abandoned | 2026-05-08 | [[2026-05-08 Crypto-carry abandoned]] |
| pullback | [[crypto-bot]] | 🔴 killed | earlier 2026 | Sharpe -4.31 |
| breakout | [[crypto-bot]] | 🔴 killed | earlier 2026 | sub-sample |

(plus Automation_MVP Telegram poller dead since March 2026 — not strict strategy but related dormant state)

## Gate condițiilor

Înainte de a re-activa o strategy killed, TOATE condițiile trebuie bifate. TBD: detail condițiile exacte din `Trading Bots/CLAUDE.md` cu o citire completă la următorul atins. Per memorie + context inferat:

- [ ] **Root cause documentat** — de ce a fost killed inițial; nu doar "Sharpe X<0", ci mecanismul fundamental care a făcut strategy să eșueze
- [ ] **Schimbare structurală în piață identificată** — ce s-a schimbat în market structure care invalidează root cause? Dacă nimic, NU resurrect.
- [ ] **Re-research dovezi pe forward data** — backtest pe perioada de după kill date, nu doar pe perioada originală
- [ ] **Re-implement curat în alpha-lab** — NU "uncomment timer-ul vechi", ci scrie din zero cu evidence-based design
- [ ] **Promotion path respectat** — alpha-lab (R&D) → stocks-bot paper → shadow → micro-live → live (NU sări peste etape)
- [ ] **Risk budget separat** — sizing prudent, NU folosi tier-ul anterior automat
- [ ] **Owner approval explicit** — decizie consemnată în `20-Decisions/` cu evidence pack

## Anti-patterns interzise

❌ **"Codul vechi e încă acolo, hai să-l re-enable"** — costul de Resurrection NU e doar tehnic, e **evidence**. Cod inert e arhivă, NU pre-armed strategy.

❌ **"Piața s-a schimbat, probabil merge acum"** — fără data, e cope. Backtest forward sau NU resurrect.

❌ **"Backtestul nou arată Sharpe pozitiv"** — verifică:
- Out-of-sample, NU re-tuned pe data nouă
- Realistic execution costs (slippage modeled, NU mid-fills)
- Survivorship bias controlled
- Regime stability (Sharpe poate fi pozitiv pe perioadă scurtă noroc, nu structural)

❌ **"Crypto-carry e clasic, probabil revine"** — vezi [[2026-05-08 Crypto-carry abandoned]] § "Why it was structurally dead". Nu re-research aceeași hypothesis fără să confruntați **BIS + BitMEX 2025 findings**.

## Cross-refs

- Source: `C:\Personal\Trading Bots\CLAUDE.md` § "Strategy resurrection gate"
- [[crypto-bot]] — proiect afectat
- [[alpha-lab]] — starting point pentru orice re-research
- [[Decisions MOC]] — vezi pattern "strategy-kill" tag
