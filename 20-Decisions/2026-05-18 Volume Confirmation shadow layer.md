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

2026-05-18 runtime update:

- Deployed Strategy OS volume UI/backend to server.
  - Backup: `/root/backups/strategy-os-volume-confirmation-20260518T111513Z`
  - Service: `strategy-os.service` restarted and `/health` OK.
- Implemented and deployed `stocks-bot` runtime persistence.
  - Commit: `a72104a` in `stocks-bot`
  - Backup: `/root/backups/stocks-bot-volume-runtime-20260518T112854Z`
  - New/updated files:
    - `src/strategy/effective_policy.py`
    - `src/strategy/paper_job.py`
    - `src/strategy/paper_ledger.py`
    - `src/strategy/strategy_v1.py`
    - `scripts/run_event_cluster_v1_shadow.py`
  - Paper orders/positions/trades now preserve:
    - `volume_z_66d`
    - `event_day_return_pct`
    - `vwap_distance_pct`
    - `turnover_percentile`
- Implemented and deployed `ibkr_bot` live reader/state persistence.
  - Commit: `d564491` in `ibkr_bot`
  - Backup: `/root/backups/ibkr-bot-volume-runtime-20260518T112854Z`
  - New fields are read from `paper_orders.csv` and written into live signal `notes.volume_confirmation` plus Strategy OS alert metrics.
  - `ibkr_bot.service` restarted, active/running, one process, no recent warning entries.

Verification:

- Local `stocks-bot`: `14 passed`
- Local `ibkr_bot`: `29 passed`
- Server compile:
  - `stocks-bot`: OK
  - `ibkr_bot`: OK
- Server process:
  - `ibkr_bot.service`: active/running

Important:

- Existing server `paper_orders.csv` headers are still old because they were generated before this deploy.
- Do **not** force a manual event-cluster run just to update headers; that could emit fresh live buy alerts unintentionally.
- The next normal `stocks-bot` run will write the new columns, and the existing `ec-v1-signal-sync.service` will copy them to `/opt/ibkr_bot/data`.

Still next:

1. Let at least one normal daily stocks run generate real rows with volume fields.
2. Verify `paper_orders.csv` header contains the new columns after the next run.
3. Feed the fields into Alert Intelligence weekly pattern analysis.
4. Keep mode `shadow` for at least 30-60 days or until enough closed trades exist.

## Cross-refs

- [[Automation_MVP]]
- [[2026-05-18 Alert Intelligence deployed]]
- [[Pre-mortem Trading Bots]]
