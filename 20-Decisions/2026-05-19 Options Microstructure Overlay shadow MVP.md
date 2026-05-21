---
title: Options Microstructure Overlay shadow MVP
type: decision
date: 2026-05-19
status: implemented-shadow
tags:
  - trading
  - alpha-lab
  - options
  - strategy-os
  - shadow
---

# 2026-05-19 — Options Microstructure Overlay shadow MVP

## Decision

Implementăm analiza de options microstructure ca **read-only shadow overlay**, nu ca live options bot.

Scopul corect este să adauge context/risk peste ETF/index structural și, ulterior, peste stocks event strategies:

- `allow`
- `size_down`
- `veto`
- `avoid_breakout_chase`
- `mark_intraday_risk`

Nu are voie să:

- cumpere/vândă opțiuni;
- plaseze ordine pe ETF/underlying;
- modifice live config;
- routeze direct în `ibkr_bot`.

## Ce s-a implementat

Repo local: `C:\Personal\Trading Bots\alpha-lab\`

Branch:

```text
feat/options-microstructure-overlay-v1
```

Commituri principale:

```text
82b8970 feat(options): add microstructure overlay contracts
7a6bda9 feat(options): add chain snapshot loader
0c36e49 feat(options): build IV surface snapshots
9955a52 feat(options): build dealer exposure snapshots
60d70ea feat(options): classify microstructure overlay state
ca2d7ff feat(options): add ETF overlay backtest and Strategy OS payload
```

Module / contracte adăugate:

- `configs/universes/options_microstructure_universe_v1.yaml`
- `configs/schemas/options_chain_snapshot_v1.yaml`
- `configs/schemas/options_surface_snapshot_v1.yaml`
- `configs/schemas/options_dealer_exposure_snapshot_v1.yaml`
- `configs/schemas/options_flow_summary_v1.yaml`
- `configs/schemas/options_microstructure_overlay_v1.yaml`
- `src/data_ingestion/load_options_chain_snapshot.py`
- `src/options/build_iv_surface_snapshot.py`
- `src/options/build_dealer_exposure_snapshot.py`
- `src/options/classify_microstructure_overlay.py`
- `src/options/run_etf_options_overlay_backtest.py`
- `src/options/build_strategy_os_custom_adapter_payload.py`

Ce face acum:

1. Citește un options chain snapshot provider-agnostic.
2. Normalizează contractele și calculează `notional_open_interest`.
3. Construiește IV surface summary:
   - `atm_iv_0dte`
   - `atm_iv_7d`
   - `atm_iv_30d`
   - `term_slope_0dte_30d`
   - `put_skew_25d`
   - `call_skew_25d`
4. Construiește dealer exposure proxy:
   - `net_gex`
   - `zero_gamma_level`
   - `call_wall`
   - `put_wall`
   - `distance_to_zero_gamma_pct`
   - `net_vanna_1volpt`
5. Clasifică overlay state:
   - `NEUTRAL`
   - `PIN_RISK`
   - `VOL_EXPANSION_RISK`
   - `DATA_INSUFFICIENT`
6. Evaluează overlay peste ETF structural baseline ca risk multiplier:
   - `size_down` => `0.5`
   - `veto` => `0`
   - restul => `1`
7. Generează payload `custom_adapter_ingest_v1` pentru Strategy OS.

## Verificare locală

Targeted tests:

```text
14 passed
```

Teste acoperite:

- contracts
- chain snapshot loader
- IV surface builder
- GEX/Vanna exposure proxy
- overlay classifier
- ETF overlay backtest
- Strategy OS custom-adapter payload

Smoke local Strategy OS:

- Strategy externă creată: `Options Microstructure Overlay v1 (local smoke)`
- `strategy_id`: `strategy_c6c2340bdbe44baf905ca69adf9ebd3c`
- `run_id`: `prun_86d0a72e38404170b232cc6adba4ece4`
- `external_run_key`: `options_overlay_SPY_2026-05-18`
- ingest job: `succeeded`
- run status: `completed`
- outcome: `shadow_evidence_ready`
- lifecycle events confirmate:
  - `run_ingested`
  - `signal`
  - `decision`
  - `metric_snapshot`

Important: smoke-ul local a folosit un ETF baseline sintetic pentru validare tehnică, nu evidence real.

## Deploy server

Server:

```text
207.154.248.232
```

Deploy făcut scoped în:

```text
/root/alpha-lab
```

Nu s-a făcut `git pull`, pentru că serverul avea repo dirty cu runtime/Kronos data. Deploy-ul a fost făcut prin tar overlay doar pentru fișierele options overlay.

Backup server:

```text
/root/backups/alpha-lab-options-overlay-20260519T132042Z
```

Verificare server:

```text
14 passed in 0.54s
```

Servicii verificate active după deploy:

- `strategy-os.service`
- `ibkr_bot.service`
- `crypto-bot.service`
- `strategy-os-telegram-ops-poller.service`

Nu s-a restartat niciun serviciu, pentru că nu s-au schimbat live bots sau Strategy OS server code.

## Ce NU este încă implementat

Acesta NU este încă un produs complet de options intelligence.

Rămâne:

1. **Provider real de options data**
   - Tradier / ThetaData / Cboe / alt provider.
   - Momentan există doar loader provider-agnostic și fixture sample.

2. **Istoric real 60+ trading days**
   - Fără istoric real, overlay-ul nu poate fi promovat.
   - Minimum shadow evidence: 60 trading days, preferabil 6-12 luni istoric pentru SPY/QQQ/IWM.

3. **Trade-flow real**
   - Nu avem încă sweep/block prints.
   - Nu avem agresor-side robust.
   - Nu avem multi-leg detection.

4. **No-lookahead validation pe date reale**
   - OI EOD trebuie folosit doar după publicare.
   - Greeks/IV trebuie timestamped.
   - Semnalul de la `t` trebuie aplicat doar pe returnul `t+1`.

5. **Timer / job de producție**
   - Nu există încă service care să genereze zilnic/intraday artifacts.
   - Nu există încă ingest automat în Strategy OS production.

6. **Strategy OS UI dedicat**
   - Payload-ul custom-adapter poate fi ingerat.
   - Lipsește încă view dedicat pentru options overlay state / reasons / artifacts.

7. **Promotion path**
   - Nu poate afecta live sizing până nu trece prin evidence gate.
   - În forma actuală este strict research/shadow.

## Următorii pași

1. Alege provider de date pentru MVP:
   - minim: Tradier/ORATS snapshot;
   - mai serios: ThetaData;
   - OPRA raw doar mai târziu.
2. Adaugă data collection job pentru SPY/QQQ/IWM.
3. Rulează 60 trading days shadow.
4. Ingest automat în Strategy OS ca custom adapter.
5. Adaugă UI Strategy OS pentru:
   - current overlay state;
   - reasons;
   - confidence;
   - backtest delta vs ETF baseline;
   - stale/missing data warnings.
6. Abia după evidence pozitiv: decide dacă rămâne risk overlay sau devine lane proprie.

## Related

- [[alpha-lab]]
- [[Automation_MVP]]
- [[Strategy resurrection gate]]
- [[2026-05-18 Alert Intelligence deployed]]
- [[2026-05-18 Volume Confirmation shadow layer]]
