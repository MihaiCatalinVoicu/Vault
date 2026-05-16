---
title: IBKR 2FA reconnect protocol
type: decision
date: 2026-05-15
status: standing-rule
impact: operational
tags:
  - decision/operational
  - product/ibkr_bot
---

# 2026-05-15 — IBKR 2FA reconnect protocol

## Context

[[ibkr_bot]] rulează pe IB Gateway server-side, headless. Când conexiunea cade (network blip, IBKR maintenance, reboot), watchdog repornește Gateway. La fiecare repornire, IBKR cere 2FA via IBKR Mobile push.

Pe 2026-05-15 04:44 UTC, am primit alert "IB Gateway DOWN >5min" cu IBKR Mobile waiting for 2FA. **Am deschis app-ul IBKR Mobile și am făcut login direct.** Asta a creat sesiune duplicate → kicked imediat ("Another session already exists"). Restart loop blocant.

## Decizia / regulă standing

> [!warning] Niciodată login direct în IBKR Mobile când Gateway așteaptă 2FA.

**Protocol corect (30 secunde):**

1. **Force-quit IBKR Mobile** dacă a fost deschis (swipe up + zvârli din recent apps, sau Settings → Force Stop)
2. **Așteaptă 30-60 sec** — watchdog reîncearcă reconnect → push notification nou pe telefon
3. **Tap DIRECT pe notification** (lock screen / notification tray) — apare dialog "Allow / Deny"
4. **Tap Allow** → Gateway primește approval → reconnect → done

**NU:**
- Nu deschide app-ul IBKR Mobile prin tap pe icon
- Nu completa username/password
- Nu rulează `/start` în app

## De ce

IBKR by default permite **o singură sesiune simultaneously** per cont. Login direct în app = sesiune nouă → expulzează Gateway-ul server-side → bot offline.

Push-ul de 2FA e separate flow: doar "approve sau deny", nu creează sesiune nouă pe telefon. E exact pentru cazul Gateway-server / Mobile-approve.

## Long-term fix opțional

Client Portal web (`interactivebrokers.com/portal`) → Settings → User Settings → **"Allow multiple concurrent sessions"** → ON.

- Pro: poți să te loghezi manual pe IBKR Mobile fără să kick Gateway-ul. Util dacă vrei trading manual ad-hoc.
- Con: securitate ușor redusă (atacator cu credentials poate să-l urmărească live).
- Decizie: TBD — nu critical. Pentru moment, protocol-ul de mai sus e suficient.

## Cross-refs

- [[ibkr_bot]] — proiect afectat
- [[Telegram bots map]] — bot_id=8639 pentru ibkr_bot alerts
