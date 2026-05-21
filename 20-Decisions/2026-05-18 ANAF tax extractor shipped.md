---
title: ANAF tax extractor shipped
type: decision
project: ibkr_bot
date: 2026-05-18
status: deployed
impact: operational
tags:
  - decision/trading
  - product/trading
  - fiscal/anaf
  - tooling
---

# 2026-05-18 — ANAF tax extractor shipped

Fast-follow Tiger T5 din [[2026-05-16 Pre-mortem Trading Bots]] livrat — tool care extrage gains/losses de pe IBKR în format RON pentru declarația anuală de câștig de capital (Form D200).

## De ce

Romania resident trading via IBKR → declarație anuală obligatorie. Fără tooling → scramble în Aprilie 2027 când vin deadline-ul + nesomnul + risc penalty întârziere. Pre-mortem-ul a flagat asta ca fast-follow tiger (T5).

## Deliverables

| Fișier | Rol |
|---|---|
| `tools/anaf_tax_export.py` | Stdlib-only Python script — parser Flex XML + FIFO matcher + BNR rate fetcher + CSV writers |
| `docs/anaf-tax-prep.md` | Workflow uman: cum descarci Flex statement, cum rulezi script-ul, ce dai contabilului |
| `tests/test_anaf_tax_export.py` | 15 cases — Flex variants, FIFO multi-lot, BNR weekend fallback, end-to-end |

## Cum funcționează

1. Citește IBKR Activity Flex XML (sursa audit-grade, NU state.json bot-internal)
2. FIFO-match buys → sells per (symbol, currency) → realized lots cu cost basis + commission split pro-rata
3. Fetch BNR daily reference rates din feed-ul oficial XML (`https://www.bnr.ro/files/xml/years/nbrfxrates<YYYY>.xml`)
4. Aplică BNR rate pe **data vânzării** (convenția HG 1/2016) pentru convert USD → RON
5. Emit 2 CSVs: per-trade detail + annual summary cu **GRAND_TOTAL_RON**

## Decizii tehnice

- **Sell-date BNR rate** (nu buy-date, nu weighted-average) — convenție ANAF default
- **FIFO cost basis** — IBKR default + safe default acceptat ANAF când brokerul nu specifică
- **Stdlib only** — fără pip deps, rulează pe orice Python 3.10+
- **Fallback weekend/holiday** — dacă data exactă n-are rate publicat, urcă cu până la 30 zile la cea mai recentă rate anterioară (convenție Norma metodologică Cod fiscal)

## Limitări declarate explicit în docstring

- Long-only stocks (EC_V1 e long-only → OK azi)
- Fără shorts, options, dividends, interest (pull separat Cash Activity report pentru ele)
- Fără handling corporate actions (splits, mergers)
- Tool ≠ legal advice — necesită consultant fiscal pentru confirmare structură (PFA / SRL / personal)

## Verificare

- 15/15 tests green pe Python 3.12 server
- BNR live URL pentru 2026 verificat reachable (125kb XML)
- NU rulează server-side (e utility, nu serviciu) — rulează pe laptop la tax time

## Workflow operatorial (când vine April 2027)

1. Client Portal → Reports → Flex Queries → Create "Activity Flex" XML annual
2. Download XML
3. Rulează: `python tools/anaf_tax_export.py --flex-xml ... --year 2026 --out-dir ...`
4. Trimite ambele CSVs la contabil
5. Contabilul face Form D200 + Anexa C-D + aplică rate (10% sau ce structură ai)

## Git

- Branch `feat/t5-anaf-tax-extractor`, commit `82fb047`
- Alt thread a stivuit ulterior 2 commit-uri unrelated peste (Verified IBKR portfolio snapshot + Telegram quantity override) — nu afectează T5

## TODO user-side

- [ ] Decide structura fiscală pentru trading (PFA vs SRL passthrough vs personal câștig de capital) cu consultant — vezi nota din [[2026-05-21 NOVENTRA accounting and funding admin]] dacă există context conex
- [ ] În aprilie 2027: rulează workflow + send la contabil

## Cross-refs

- [[ibkr_bot]]
- [[2026-05-16 Pre-mortem Trading Bots]]
- [[2026-05-17 Pre-mortem T1 T2 T3 mitigations deployed]]
