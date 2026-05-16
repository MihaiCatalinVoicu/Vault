---
title: Pre-mortem skill cherry-pick from borghei
type: decision
date: 2026-05-16
status: executed
impact: minor
tags:
  - decision/tooling
  - decision/claude-skills
---

# 2026-05-16 — Pre-mortem skill cherry-pick (borghei)

## Context

User a întrebat despre repo `https://github.com/borghei/Claude-Skills`. Verificarea WebFetch a arătat 3 red flags vs [[2026-05-16 Setup Obsidian vault|kepano]]:

1. Auto-modify CLAUDE.md în install (prompt injection vector)
2. MCP server custom + Python tools + npm package executabile (NOT pure declarative)
3. 266 skills + 674 tools în 17 domenii = quantity over quality + autor neverificat

Recomandarea inițială: **NU instala full repo**. Dar user a întrebat punctual de `pre-mortem` skill — o tehnică legit de risk planning (Gary Klein 2007).

## Decizia

**Cherry-pick selective doar pre-mortem skill**, fără MCP, fără npm install, fără rest.

## Audit done înainte de install

| Verificare | Rezultat |
|---|---|
| SKILL.md crafted vs LLM boilerplate | ✅ Crafted — Klein/Kahneman/Heath/Edmondson refs, framework Tigers/Paper Tigers/Elephants, examples specifice, troubleshooting concret |
| `risk_categorizer.py` cod safety | ✅ Pure stdlib (`argparse`/`json`/`sys`/`typing`), zero network, zero subprocess, zero file write/eval/exec, zero side effects |
| Asset / reference files | ✅ pre_mortem_template.md (3 KB) + pre-mortem-guide.md (10 KB) — pure markdown |
| Stefanini risk | ✅ Zero exfiltration capability, runs local-only |
| Collision risk în `~/.claude/skills/` | ✅ Prefix `borghei-` evită collision |

## Install path

- **Source sandbox:** `C:\Personal\claude-resources\borghei-claude-skills\project-management\discovery\pre-mortem\` (read-only clone, NOT installed wholesale)
- **Target:** `~/.claude/skills/borghei-pre-mortem/` (4 fișiere: SKILL.md + script + template + guide)

## Standing rule

> [!info] Pentru orice repo skills terț: read SKILL.md + audit scripts pentru network/subprocess/eval ÎNAINTE de install. Pattern `kepano-*` și acum `borghei-*` păstrează provenance clar.

> [!warning] NU `npx`-uit, NU rulat MCP server, NU lăsat auto-modify CLAUDE.md.

## Concept summary (pentru recall rapid)

Pre-mortem = imaginezi că proiectul a eșuat și gândești înapoi.

**Trei categorii de risk:**
- **Tigers** = risc real cu evidence (e.g. "Auth service a căzut 3 ori luna trecută")
- **Paper Tigers** = sound scary dar improbabile/low-impact ("Competitor might copy")
- **Elephants** = unspoken concerns ("Tech lead nu crede în proiect, e disengage")

**Urgency tigers:** Launch-Blocking | Fast-Follow | Track

**Când invoci:** înainte de [[2026-05-06 Client app market readiness sprint plan|major launch]], înainte de strategie nouă pe [[crypto-bot]], înainte de architectural change.

## Cross-refs

- [[2026-05-16 Setup Obsidian vault]] — installation a kepano skills pattern
- Source repo audit: NU install full borghei/Claude-Skills (red flags)
- Skill location: `~/.claude/skills/borghei-pre-mortem/`
