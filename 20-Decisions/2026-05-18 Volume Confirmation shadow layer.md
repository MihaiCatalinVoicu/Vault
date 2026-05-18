---
title: Volume Confirmation shadow layer
type: decision
date: 2026-05-18
status: implemented
impact: research
tags:
  - decision/trading
  - product/strategy-os
  - strategy/stocks
  - research/volume
---

# 2026-05-18 - Volume Confirmation shadow layer

## Source

PDF reviewed:

`C:\Users\mvoicu1\Downloads\From_Paper_to_Strategy_Vol_001_Volume.pdf`

Relevant ideas extracted:

- VWAP is useful as intraday pressure/context, but the paper's VWAP system is a day-trading QQQ/TQQQ strategy and should not be copied into EC_V1 live.
- Abnormal volume is useful as an event confirmation layer: volume z-score above a rolling baseline plus same-day price confirmation may indicate information flow.
- Turnover is better than raw volume for cross-asset comparisons.

## Decision

Add volume as a **shadow/context layer** for stocks, not as a live gate.

This means the platform records and displays volume confirmation evidence, but it does not block/execute trades based on it until enough outcome history exists.

## Implemented

Repo: `Automation_MVP`

Backend:

- `strategy_os/stocks_policy.py`
  - added `volume_confirmation` to `stocks_event_strategy_v1`;
  - default mode is `shadow`;
  - defaults:
    - `volume_baseline_days=66`
    - `abnormal_volume_z_threshold=2.33`
    - `price_confirmation_pct=3.0`
    - `vwap_context_mode=shadow`
    - `turnover_context_mode=shadow`
  - effective policy export now includes the volume layer;
  - policy explanation includes the volume rule summary.

- `strategy_os/stocks_evidence.py`
  - added `volume_confirmation` evidence aggregation;
  - accepts columns such as:
    - `volume_z_66d`
    - `event_day_return_pct`
    - `vwap_distance_pct`
    - `turnover_percentile`
  - reports abnormal volume count, price-confirmed abnormal volume count, above-VWAP count, average turnover percentile, and by-status buckets.

Frontend:

- Strategy OS panel, Stocks Policy editor:
  - added `Volume Confirmation` controls.
- Stocks Evidence Dashboard:
  - added `Volume Confirmation` section with summary and recent rows.

Tests:

- `tests/test_stocks_policy_control_plane.py`
  - verifies policy export includes volume confirmation shadow layer.
- `tests/test_stocks_evidence_dashboard.py`
  - verifies volume confirmation evidence aggregation.

Verification:

- `pytest tests/test_stocks_policy_control_plane.py tests/test_stocks_evidence_dashboard.py -q`
  - `5 passed`
- `node --check strategy-os/frontend/app.js`
  - OK

## Important constraint

Do not promote this to a hard live gate yet.

Next correct step is to let the runtime populate these fields for real EC_V1 signals and evaluate:

- executed winners vs losers by volume confirmation;
- skipped signals with volume confirmation;
- MAE/MFE by `volume_z_66d`;
- outcome by VWAP context;
- whether turnover improves cross-symbol comparability.

## Next

1. Wire `stocks-bot` / `ibkr_bot` runtime to calculate and persist:
   - `volume_z_66d`
   - `event_day_return_pct`
   - `vwap_distance_pct`
   - `turnover_percentile`
2. Keep mode `shadow` for at least 30-60 days or until enough closed trades exist.
3. Feed volume confirmation patterns into Alert Intelligence weekly consensus.

## Cross-refs

- [[Automation_MVP]]
- [[2026-05-18 Alert Intelligence deployed]]
- [[Pre-mortem Trading Bots]]
