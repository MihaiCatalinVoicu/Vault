---
title: stocks-bot
type: project
status: paper
risk: sim
stack:
  - Python
  - Typer CLI
  - SEC EDGAR (Form 4)
  - Docker compose
tags:
  - project/active
  - product/research
  - risk/sim
  - venue/stocks
created: 2026
updated: 2026-05-16
---

# stocks-bot

🟢 **PAPER only (SIM)** — research pipeline pe acțiuni bazat pe SEC Form 4 (insider transactions). Generează semnale, NU execută real direct. Semnalele EC_V1 alimentează [[ibkr_bot]] (cuplaj).

## On-disk

`C:\Personal\Trading Bots\stocks-bot\`

## Architecture

```
Ingest SEC Form 4 → Normalize → Events (company-day aggregates)
     → Enrich (fundamentals/technicals) → Score (risk gate + ranker)
     → Generate Ideas → Simulate (shadow) → Evaluate
```

## Key configs

| File | Rol |
|---|---|
| `configs/lane_registry.yaml` | All lanes: `stocks_event_v2_lane` (canonical), `stocks_driver_heavy_legacy` (benchmark), `crypto_crowding_unwind_lane` (planned), `parity_v1` (Polymarket — frozen) |
| `configs/promotion_controller.yaml` | Lane promotion rules (paper → micro-live → live) |
| `configs/canonical_profiles.yaml` | Profile templates (EVENT_CLUSTER_V1, PORTFOLIO_SHADOW_LONG, etc.) |
| `configs/driver_heavy_portfolio*.yaml` | Driver-heavy portfolio configs (legacy benchmark) |

## Active lanes (per lane_registry)

| Lane | State | Notes |
|---|---|---|
| `stocks_event_v2_lane` | 🟢 active | Canonical event-cluster lane, target for shadow + micro-live promotion |
| `stocks_driver_heavy_legacy` | 🟡 passive | Legacy benchmark only, kept for comparison |
| `crypto_crowding_unwind_lane` | ⚪ planned | Research-only until venue alignment + execution truth coherent |
| `parity_v1` (Polymarket) | 🔴 frozen | Frozen on shared host until runtime mapping + resource isolation fixed |

## Coupling cu ibkr_bot

stocks-bot **PAPER** generează semnale → [[ibkr_bot]] **LIVE** execută. Asta înseamnă schimbări în stocks-bot scoring pot afecta live trades. Înainte de orice merge pe `main` care atinge `risk gate`, `ranker`, sau `lane_registry` — review în context cu ibkr_bot side.

## Setup

```bash
cp .env.example .env
# Edit .env cu SEC User-Agent, optional API keys
pip install -e .
python -m src.app ingest --since 7d
```

## Cross-refs

- Live consumer: [[ibkr_bot]] (EC_V1 strategy)
- Sibling research: [[alpha-lab]] (deep research, separate from live)
- Sibling venue: [[Pollymarket]] (parity_v1 lane frozen here references it)
