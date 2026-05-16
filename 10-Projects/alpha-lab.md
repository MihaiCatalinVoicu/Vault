---
title: alpha-lab
type: project
status: active
risk: sim
stack:
  - Python
  - pyproject.toml managed
  - Jupyter notebooks
tags:
  - project/active
  - product/research
  - risk/sim
created: 2026
updated: 2026-05-16
---

# alpha-lab

🟢 **R&D (SIM)** — separate research laboratory pentru discovering, evaluating, și promoting trading edge fără coupling la live systems.

## On-disk

`C:\Personal\Trading Bots\alpha-lab\`

## Purpose

- Izolează edge research de live runtimes ([[ibkr_bot]], [[crypto-bot]])
- Construiește reusable research assets, NU one-off strategy experiments
- Forțează fiecare idea nouă prin **same evidence + realism gates**

## Non-goals (CRITICAL — NU încălca)

> [!warning] NU live order routing din alpha-lab.

- ❌ Niciun live order routing
- ❌ Niciun read/write în live runtime state al bot-urilor existente
- ❌ Niciun UI / dashboard
- ❌ NU înlocuieste live bots — strategii promovate trec prin `stocks-bot` shadow / `ibkr_bot` paper-to-micro-live workflow

## Research Fronts (active)

1. **ETF / index structural** — long-horizon structural edges în indexes
2. **SEC event lane** — events-based stocks (probably feed la [[stocks-bot]] cluster lane)
3. **Crypto carry / basis** — ⚠️ **research-only**, fără implicații pe [[crypto-bot]] dormant; vezi [[2026-05-08 Crypto-carry abandoned]] pentru de ce carry classic e dead structural

> [!note]
> Crypto carry/basis în alpha-lab e research FRONT, NU revivare strategy. Dacă cercetarea dovedește mecanism nou care nu mai e impacted de market structure 2024+, atunci consider strategy resurrection cu gate strict.

## Layout

| Dir | Rol |
|---|---|
| `strategies/` | Strategy implementations (sim only) |
| `notebooks/` | Jupyter notebooks pentru exploration |
| `scripts/` | Pipeline scripts (data prep, backtest runners) |
| `data/` | Local cache pentru historical data |
| `artifacts/` | Backtest outputs, plots, metrics |
| `configs/` | Strategy configs |
| `ops/` | Operational scripts |
| `tests/` | Unit tests |
| `docs/` | Internal docs |

## Promotion path (research → live)

`alpha-lab` strategy → manual review → re-implement în [[stocks-bot]] (paper) → shadow → micro-live → live (consumed by [[ibkr_bot]])

Trecerea NU e automată. E gating manual (CLAUDE.md menționează "Strategy resurrection gate" cu condiții explicite).

## Cross-refs

- Consumer pipeline: [[stocks-bot]] (paper) → [[ibkr_bot]] (live)
- Sibling (separate research): [[research-orchestrator]] (idea review/debate)
- Killed (DO NOT auto-resurrect): [[2026-05-10 Lane A funding-contrarian killed]], [[2026-05-08 Crypto-carry abandoned]]
