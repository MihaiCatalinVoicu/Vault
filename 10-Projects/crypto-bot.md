---
title: crypto-bot
type: project
status: dormant
risk: off
stack:
  - Python
  - Binance API
  - ML pipeline (crypto-ml submodule)
tags:
  - project/dormant
  - product/trading
  - risk/off
  - venue/binance
created: 2026
updated: 2026-05-18
---

# crypto-bot

🛑 **DORMANT strategy-wise (2026-05-10)** — toate strategiile killed, serviciul încă rulează pentru scan/outcome/observability.

## On-disk

- **Local repo:** `C:\Personal\Trading Bots\Crypto_bot\` (note: folder name capitalized în filesystem, dar canonical `crypto-bot` în docs)
- Long-term plan notes (free-text): `crypto bot plan termen lung.txt`

## Structure

| File | Rol |
|---|---|
| `main.py` | Entry point bot |
| `funding_contrarian.py` | 🔴 Lane A strategy — killed |
| `ml_pipeline.py` + `ml_gate.py` + `ml_risk_gate.py` | ML decision layer |
| `risk_gate.py` + `risk_manager.py` | Risk controls |
| `trade_logger.py` | Audit trail |
| `crypto-ml/` | Subdirectory cu ML model training |
| `docs/LANE_A_FUNDING_CONTRARIAN_DESIGN.md` | Design doc Lane A (📜 arhivă) |

## Telegram bot

bot_id = **8736** (post swap 2026-05-08). Vezi [[Telegram bots map]].

## Strategy history — TOATE killed

| Strategy | Status | Why killed | Date | Detalii |
|---|---|---|---|---|
| **crypto-carry** | 🔴 ABANDONED | structural dead per BIS + BitMEX 2025 research | 2026-05-08 | [[2026-05-08 Crypto-carry abandoned]] |
| **pullback** | 🔴 KILLED | Sharpe -4.31 backtest | earlier 2026 | TBD detail file |
| **breakout** | 🔴 KILLED | sub-sample, unreliable | earlier 2026 | TBD detail file |
| **Lane A funding-contrarian** | 🔴 KILLED | Sharpe -1.14 over 18mo backtest | 2026-05-10 | [[2026-05-10 Lane A funding-contrarian killed]] |

## Current state

- Service alive (process running)
- 2026-05-18: mirror către Strategy OS Alert Intelligence pentru signal/decision/exit events
- `decision_log.csv` și `trades.csv` sunt importate în Strategy OS pentru outcome history
- Telegram issue deschis: token valid pentru `CryptoBotSimBot`, dar `getChat(1194534321)` returnează `chat not found`; probabil trebuie `/start` pe botul crypto specific
- Cod inert pentru strategies killed — păstrat ca paper trail/arhivă

## 🔴 Strategy resurrection gate

Per `Trading Bots/CLAUDE.md`: NU re-activa nimic fără TOATE condițiile (TBD detail în reference file).

## Hyperliquid + HLP onboarding

User gestionează onboarding cu ghidul la `Trading Bots/docs/hyperliquid-onboarding.md`. $50-100 capital planned. **Alt approach decât crypto-carry** — vezi ghid pentru detalii.

## Next steps (TBD, fără urgență)

- Decide R&D direction (momentum reversal? cross-exchange arbitrage? alt mechanism?)
- Re-examine 2024-2026 market structure (instituționalization compressing edges)
- Set sizing prudent dacă re-pornește

## Cross-refs

- Killed strategies: [[2026-05-10 Lane A funding-contrarian killed]] · [[2026-05-08 Crypto-carry abandoned]]
- Research lab (still alive, decoupled): [[alpha-lab]]
- Related (different venue): [[ibkr_bot]]
