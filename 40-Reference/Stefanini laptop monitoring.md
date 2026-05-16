---
title: Stefanini laptop monitoring
type: reference
tags:
  - reference/security
  - reference/corporate
updated: 2026-05-16
---

# Stefanini laptop monitoring

Context: laptop primit de la Stefanini (consultancy IT). Domain user `ROM_EU_TTGLOBAL\mvoicu1`. Setup confirmat empiric via Task Manager + Get-Service + Get-Process pe 2026-05-16.

## Monitoring stack confirmat

| Tool | Rol | Capabilitate observare |
|---|---|---|
| **Zscaler Client Connector** (4 procese + kernel driver `zapprd`) | Web traffic interceptor (Secure Web Gateway) | **Tot HTTPS traffic decriptat** prin TLS inspection (cert root corporate injectat în trust store Windows). Vede URLs, request body, response body |
| **Sophos Endpoint** (10+ procese: McsAgent, McsClient, NetFilter, NtpService, **Osquery**, FileScanner, EndpointDefense + kernel mini-filter) | EDR + AV + Network threat protection | File create/modify events, process launches, DNS, network connections |
| **🔴 Rapid7 Insight Agent** (`ir_agent`) | SIEM + UEBA (User Behavior Analytics) | **Process events cu command-line args** (incluzând `-storepass`!), log forwarding, authentication events, file hashes, forensic timeline |
| Microsoft Intune Management Extension | Asset management + policy enforcement | Admin poate rula scripts arbitrari pe device, install/remove apps, registry changes |
| Microsoft Defender + WebThreatDef | Built-in AV + anti-phishing | Standard process & file scanning |
| Adaptiva Client Service | RMM (content distribution, patches) | Software push, inventory |
| Dell TechHub / TrustedDevice / SupportAssist | Laptop vendor management | Less invasive, present |

## Ce N-am găsit (rămâne semn bun)

- ❌ Niciun keylogger explicit (Teramind, Veriato, ActivTrak, Hubstaff)
- ❌ Niciun DLP heavy (Forcepoint, Digital Guardian, Code42, Symantec DLP)
- ❌ Niciun screen recorder dedicat
- ❌ Niciun CrowdStrike Falcon / SentinelOne / Cylance

## Vector real de leak

### Process command-line args

Rapid7 InsightIDR captures process command-line at launch by default. Pentru:

```bash
keytool -genkeypair ... -storepass "PAROLA" -keypass "PAROLA" ...
```

→ parola apare în InsightIDR logs, **retroactiv accesibilă** admin-ilor Stefanini.

**Mitigare:** NU rula `keytool` cu `-storepass` în plain text pe Stefanini. Alternativ — pe laptop personal generezi keystore-ul.

### File system audit

Sophos Mini-Filter driver vede file create/modify. Crearea `key.properties` (cu parolă plain text) + `*.keystore` = events logged.

**Mitigare:** keystore + parolă pe laptop personal numai.

## Ce e SAFE pe Stefanini

- Code commits (open source GitHub oricum)
- Edit `.md`, `.tsx`, `.jsx`, `.java` files
- Build Vite frontend (`npm run build`)
- Run Docker local (pentru testing)
- Browse GitHub, Stack Overflow, public docs (Zscaler vede, dar nimic sensitiv)
- Edit acest vault (NU conține secrete, e knowledge management)

## Ce NU e safe

- Generare keystore producție / signing keys
- Login cont Play Console SRL (identity stream comercial)
- Login cont bancar personal sau cripto exchanges
- Open password vault personal cu master password
- Editare fișiere cu PII clienți reali

## Standing rule

> [!important] Pentru orice activitate care expune **parole, secrete, identity comercială** → migrează pe laptop personal.

## Cross-refs

- [[2026-05-06 Client app market readiness sprint plan]] — exemplu de corporate boundary applied
- [[Claude session boundary]] — cum afectează cum lucrez eu (Claude) pe acest laptop
