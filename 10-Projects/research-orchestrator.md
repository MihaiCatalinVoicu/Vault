---
title: research-orchestrator
type: project
status: tooling
risk: sim
stack:
  - Python
  - Typer CLI
  - Docker compose
  - Postgres (via docker-compose)
tags:
  - project/active
  - product/tooling
  - product/research
  - risk/sim
created: 2026
updated: 2026-05-16
---

# research-orchestrator

🟢 **TOOLING (SIM)** — orchestration service separate pentru idea review, debate, voting, outcome tracking. **Deployed 2026-05-08** ca **A1 digest** (referit în memory pentru [[ibkr_bot]] context — A1 digest = multi-venue digest produs de acest serviciu).

## On-disk

`C:\Personal\Trading Bots\research-orchestrator\`

## Scope

> [!info] Intentionally isolated de [[crypto-bot]] și [[stocks-bot]].
> NU controlează direct live trading systems în primul milestone.

Owns:
- Idea + context storage (Postgres)
- Agent registry + calibration metadata
- Debate sessions, responses, votes
- Arbiter decisions
- Later: experiment linking, postmortems, meta-features

## Architecture

Multi-agent system pentru research validation:
- **Agents** = entities cu diverse perspective (calibrated cu metadata)
- **Ideas** = trade hypotheses propuse pentru evaluation
- **Debate** = structured exchange between agents on an idea
- **Voting** = collective scoring after debate
- **Arbiter** = final decision-maker

## A1 digest

Deployed 2026-05-08. Multi-venue digest care agregează info cross-venue (probably [[ibkr_bot]] + [[stocks-bot]] + maybe [[Pollymarket]] data) pentru daily/intra-day summary. Detail TBD when next touch.

## Quick start

```powershell
.\.venv\Scripts\python.exe -m pip install -e .[dev]
docker compose up -d
.\.venv\Scripts\python.exe -m src.app init-db
.\.venv\Scripts\python.exe -m src.app seed-agents
.\.venv\Scripts\python.exe -m src.app create-sample-idea
```

## Layout

| Dir | Rol |
|---|---|
| `src/` | Service code |
| `migrations/` | Postgres schema migrations |
| `scripts/` | Helper scripts |
| `task_specs/registry.yaml` | Task spec registry |
| `data/` | Local data cache |

## Cross-refs

- Producer of A1 digest consumed by: [[ibkr_bot]] (referenced in memory)
- Adjacent research: [[alpha-lab]] (lower-level strategy R&D)
- Live consumers: [[stocks-bot]] (signal gen), [[ibkr_bot]] (live exec)
