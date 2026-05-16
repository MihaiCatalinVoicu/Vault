---
title: Pollymarket
type: project
status: tbd
risk: tbd
stack:
  - Python
  - pyproject.toml managed
  - Polymarket CLOB API
tags:
  - project/tbd
  - product/trading
  - venue/polymarket
created: 2026
updated: 2026-05-16
---

# Pollymarket

🟡 **TBD status** — per `Trading Bots/CLAUDE.md`: "Verifică status când atingi". Codename `polymarket_mm_v1` — reward-aware passive market making pe binary objective markets, cu full-set parity monitoring + latency-sensitive repricing overlay.

> Note: folder name spelled "Pollymarket" (double L) în filesystem, dar CLI și codebase folosesc "polymarket". Convențional: discutăm ca "Polymarket".

## On-disk

`C:\Personal\Trading Bots\Pollymarket\`

## Strategy: `polymarket_mm_v1`

Reward-aware passive market making pe **fee-enabled, orderbook-enabled, objective binary markets cu rules clare**.

Phased rollout:
1. **One-sided passive** (start)
2. **Two-sided passive** (după ce one-sided e stable)
3. **Latency overlay** pe smaller subset (după ce two-sided e proven)

## Scope freeze (CRITICAL — NU extinde)

> [!warning] NU trade pe astea:
> - Sports
> - Politics
> - Geopolitics
> - Augmented neg-risk placeholders
> - `Other` category
> - Taker-only news markets
> - Generic narrative probability strategies

Trade NUMAI pe: fee-enabled + orderbook-enabled + objective binary cu rules clare.

## Layout

| Dir | Rol |
|---|---|
| `src/discovery/` | Market discovery + eligibility filtering |
| `src/marketdata/` | Polymarket CLOB API client |
| `src/execution/` | Order placement |
| `src/inventory/` | Inventory tracking |
| `src/registry/` | Market registry snapshots |
| `src/risk/` | Risk controls |
| `src/settlement/` | Settlement logic |
| `src/ops/` | Ops scripts |
| `src/common/` | Shared utilities |
| `configs/market_allowlist.yaml` | Allowlist hardcoded |
| `configs/market_denylist.yaml` | Denylist hardcoded |
| `configs/promotion_controller.yaml` | Phase rollout rules |

## Cross-link cu stocks-bot

[[stocks-bot]] are `parity_v1` lane (Polymarket) marcat **frozen** — "Frozen on shared host until runtime mapping and resource isolation are fixed". Asta sugerează că Pollymarket are issue de coexistență cu alte lanes pe același host.

## Workflow CLI

```bash
python -m src.app fetch-markets --limit 50 --output data/registry/raw_markets.json
python -m src.app build-registry --raw data/registry/raw_markets.json --output data/registry/market_registry_snapshot.json
python -m src.app filter-eligible --snapshot data/registry/market_registry_snapshot.json --output data/registry/eligible_markets_latest.json
python -m src.app emit-run-manifest --metrics data/reports/sample_metrics.json --output data/runtime/run_manifests/sample_run_manifest_v1.json
```

## Open questions

- Status azi: live? paper? frozen?
- Capital allocation?
- Telegram bot integration?

**TODO:** verifică la următoarea sesiune când atingem Pollymarket. Update status când e clar.

## Cross-refs

- Adjacent: [[stocks-bot]] (parity_v1 lane references this)
