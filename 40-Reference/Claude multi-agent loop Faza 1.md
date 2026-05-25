---
title: Claude multi-agent loop Faza 1
type: reference
status: active
tags:
  - reference/claude
  - reference/process
  - reference/restaurant-os
created: 2026-05-25
updated: 2026-05-25
---

# Claude multi-agent loop Faza 1

Setup pentru pattern-ul multi-agent (implementer → test-runner → reviewer → commit) în Claude Code Desktop, fără agent teams experimental și fără orchestrator extern. Acoperă regulile hard din canonicele Restaurant OS prin subagenți read-only post-edit + un Stop hook advisory.

> [!info] **De ce există asta**
> User a tras de două ori pe self-bug-ul `tail -N` care a ratat verdict-ul de test (sprint-144/145). Reviewer-ul + test-runner-ul ca subagenți separați transformă disciplina într-o STRUCTURĂ, nu act de voință. Plus: 10-check post-edit gate prinde scurgeri de manager-routes pe `:3001`, Flyway band collision, build artifacts în commit, console pollution.

## Arhitectura

```
implementer (turn curent în Claude Code)
  ↓
  edit cod
  ↓
test-runner subagent  → verdict GREEN / RED / INFRA_DOWN / AUTH_FAIL
  ↓
reviewer subagent     → verdict PASS / FAIL / NEEDS_ATTENTION
  ↓
git commit (cu pre-commit-manifest.sh hook existent)
  ↓
Stop event → reviewer-nudge.sh emite nudge pe diff non-trivial sau risky path
```

**Layered defense** existentă (PRE-edit, deja activă în repo):
- `track-guard.sh` — blochează Edit/Write pe paths cross-track
- `flyway-band-check.sh` — blochează `V*.sql` în afara benzii track-ului
- `pre-commit-manifest.sh` — manifest check pe `git commit*`
- `java-compile-check.sh` — PostToolUse Java compile

Reviewer-ul e POST-edit gate, complement layered. Nu duplicare.

## Fișiere livrate

| Path | Acțiune | Comitat în |
|---|---|---|
| `D:\reservation system\.claude\agents\reviewer.md` | create | `3d704cb` meta-repo |
| `D:\reservation system\.claude\agents\test-runner.md` | create | `3d704cb` meta-repo |
| `D:\reservation system\.claude\hooks\reviewer-nudge.sh` | create (+chmod +x) | `3d704cb` meta-repo |
| `D:\reservation system\.claude\settings.json` | modify (Stop section) | `3d704cb` meta-repo |
| `D:\reservation system\CLAUDE.md` | modify (4 diffs) | `3d704cb` meta-repo |
| `Reservation-System/docs/agent-loop/*.template.md` (5) | create | `303e6018` Reservation-System |
| `Reservation-System/.gitignore` | append filter | `303e6018` Reservation-System |

Skipped intentional:
- `.claude/skills/deploy/SKILL.md` — conține literal dev admin password (`admin@reservation.local` + 24-hex). Gitignored până la sanitization.

## reviewer.md (embedded)

```markdown
---
name: reviewer
description: Read-only reviewer for Restaurant OS. Use after non-trivial edits before commit. Checks port mapping (:3001 no staff), Flyway band V1000+/V2000+, status labels evidence, scope discipline, build artifacts, server safety. NEVER edits.
tools: Read, Glob, Grep, Bash
---

You are the Reviewer. READ-ONLY. You do not Edit, Write, or commit.

## 10 required checks (output PASS / FAIL / N/A per item)

1. Port mapping — :3001 fără staff/manager (deletions only or N/A)
2. Flyway band — V<n> în banda track-ului (restaurant V1000-1999, hotel V2000-2999, spa V3000-3999, tourist V4000-4999, shared V9000+ cu HANDSHAKE_SIGNED)
3. Flyway slot collision — git log --all --grep="claim Flyway V<n>"
4. Status labels — TESTED_PLAYWRIGHT cere .spec.ts în diff sau repo
5. Build artifacts — NU în diff: dist/**, openapi.json, build-info.json, *.log, node_modules
6. Scope discipline — launch.json/.vscode/.github/workflows fără declarație în GOAL.md
7. Server safety — niciun string 207.154.248.232 (Trading server, proiect diferit)
8. Contract change rebuild — openapi.yaml / *Controller.java cu @*Mapping / DTO field → 3 FE rebuild advisory
9. Console clean — no newly added console.log/error în .tsx/.ts
10. ACCEPTANCE match — fiecare item COVERED/NOT_COVERED based on diff

## Severity rules
- BLOCKING: FAIL on items 1, 2, 3, 5, 7
- NEEDS_ATTENTION: items 4, 6, 8, 9, 10
```

## test-runner.md (embedded — esență)

```markdown
---
name: test-runner
description: Runs targeted test suites with pipe-to-file + grep summary discipline. NEVER edits source code.
tools: Bash, Read, Grep
---

## Pre-execution discovery (HARD)

Frontend workspaces:
- READ <workspace>/package.json ÎNAINTE de a rula npm/npx
- Folosește scripturile existente (test, typecheck, build) — NU inventa
- N/A dacă scriptul așteptat lipsește, cu lista scripts disponibile

Backend (Maven):
- Fallback chain: ./mvnw → ./mvnw.cmd (Windows/Git Bash) → mvn (system PATH)
- INFRA_DOWN dacă niciuna nu funcționează

## Execution discipline
1. Pipe la fișier: <cmd> > /tmp/test-run-$$.log 2>&1; echo "exit=$?"
2. NU tail -N pe output (summary line se pierde — sprint-144/145 bug)
3. grep -E "Tests run|BUILD SUCCESS|BUILD FAILURE|FAIL|ERROR" /tmp/test-run-$$.log | tail -20
4. Summary line VERBATIM. Cuvinte interzise: "should work", "looks good", "all green"

## Verdict semantics
- GREEN — exit 0, summary 0 failures, build SUCCESS
- RED — exit !=0 cu test failures
- INFRA_DOWN — env issue (Docker down, port collision, Maven missing)
- AUTH_FAIL — login fixture failed, bootstrap drift
```

## reviewer-nudge.sh (Stop hook)

```bash
#!/bin/bash
# Faza 1: advisory-only. No exit 2 / blocking (avoids deadlock without verdict marker).
# $CLAUDE_AUTO_REVIEW=1 increases verbosity on risky paths; does NOT block Stop.

REPO_ROOT="D:/reservation system/Reservation-System"
cd "$REPO_ROOT" 2>/dev/null || exit 0

# Combined staged + unstaged diff vs HEAD — catches risky unstaged even if trivial staged
diff_stat=$(git diff HEAD --numstat 2>/dev/null)
[ -z "$diff_stat" ] && exit 0

total_lines=$(awk '{ if ($1!="-" && $2!="-") sum += $1+$2 } END { print sum+0 }' <<< "$diff_stat")
paths=$(git diff HEAD --name-only 2>/dev/null)

risky=0; risky_reason=""
while IFS= read -r p; do
  [ -z "$p" ] && continue
  case "$p" in
    *Controller.java|*ControllerImpl.java) risky=1; risky_reason="contract change ($p)";;
    *openapi.yaml|*openapi.yml)            risky=1; risky_reason="API spec ($p)";;
    *db/migration/V*.sql)                  risky=1; risky_reason="Flyway migration ($p)";;
    *auth/*|*Authentication*|*Authorization*) risky=1; risky_reason="auth surface ($p)";;
    *billing/*|*Billing*)                  risky=1; risky_reason="billing surface ($p)";;
    *fiscal/*|*efactura/*|*e-factura/*)    risky=1; risky_reason="fiscal/e-Factura ($p)";;
  esac
done <<< "$paths"

# Trivial (<30 lines, no risky paths) → silent
[ "$risky" -eq 0 ] && [ "$total_lines" -lt 30 ] && exit 0

if [ "$risky" -eq 1 ]; then
  if [ "${CLAUDE_AUTO_REVIEW:-0}" = "1" ]; then
    echo "[reviewer-nudge] RISKY PATH ($risky_reason) — reviewer subagent STRONGLY required."
  else
    echo "[reviewer-nudge] Risky path touched ($risky_reason). Recommend reviewer subagent."
  fi
  exit 0
fi

echo "[reviewer-nudge] Non-trivial diff ($total_lines lines). Consider reviewer subagent."
exit 0
```

## CLAUDE.md root stub — 4 diffs aplicate

**Diff 1** — bullet `Test verification`:
> după ORICE fix non-trivial (BE/FE multi-fișier, contract change, migration), invocă subagent `test-runner`. Pentru single-file <30 LOC, re-run direct.

**Diff 2** — bullet `Commit hygiene` (append):
> Pentru task-uri non-triviale: rulează subagent `reviewer` înainte de `git commit` și citește verdict-ul ca gate, nu doar `git diff --cached`.

**Diff 3** — secțiune nouă `## Multi-agent orchestration (Faza 1 LIVE)` între Reguli execuție și Skill-uri. Detalii: bucla, reviewer/test-runner, hybrid Stop hook, manual invocation, scratchpad per-task, Faza 2 deferred.

**Diff 4** — `## Skill-uri și agenți` (append 2 bullets):
> - Code review pre-commit (multi-fișier, Flyway/contract change, risky paths): subagent `reviewer`.
> - Test suite execution cu summary discipline: subagent `test-runner`.

Revizie: `2026-05-25` adăugată la footer.

## settings.json Stop section (wiring)

```json
"Stop": [
  {
    "matcher": "",
    "hooks": [
      { "type": "command", "command": "echo '[hook] Turn complete'", "timeout": 5 },
      { "type": "command", "command": "bash 'd:/reservation system/.claude/hooks/reviewer-nudge.sh'", "timeout": 10 }
    ]
  }
]
```

Restul 6 hooks (PostToolUse java-compile-check, PreToolUse pre-commit-manifest + docker-volume-guard + track-guard + flyway-band-check, UserPromptSubmit skill-nudge) NEATINSE.

## Scratchpad per-task

`Reservation-System/docs/agent-loop/` (gitignored runtime, `*.template.md` committed):
- `GOAL.md` — scope task curent (track, pilon, surface, Flyway slot)
- `STATE.md` — slices done + currently + blockers
- `ACCEPTANCE.md` — DoD verificabil (BE tests, FE typecheck, migration, reviewer verdict, console clean, no build artifacts)
- `RUN_LOG.md` — append-only audit trail
- `DECISIONS.md` — append-only, alternative respinse + trade-off

Populezi la început de task non-trivial; subagenții le citesc.

## Cum invoci manual

```
Agent({subagent_type: "reviewer", prompt: "Review staged diff against ACCEPTANCE.md"})
Agent({subagent_type: "test-runner", prompt: "Run backend suite"})
```

`$CLAUDE_AUTO_REVIEW=1` în shell crește verbozitatea nudge-ului pe risky paths (NU blochează).

> [!warning] **Subagenții se încarcă la session start**
> După edit pe `.claude/agents/*.md`, Claude Code TREBUIE restartat (kill process + reopen, NOT `/clear`). Verifici cu `/agents` slash command în TUI — `reviewer` și `test-runner` trebuie să apară în Library.

## Faza 2 deferred path

**Blocking Stop hook fără deadlock**: reviewer scrie marker gitignored `Reservation-System/docs/agent-loop/.reviewer-verdict` cu `diff_hash=<sha1 of git diff HEAD> verdict=PASS|FAIL timestamp=<iso>`. Hook calculează hash actual:
- trivial → exit 0
- marker există + hash egal + verdict=PASS → exit 0
- altfel + `$CLAUDE_AUTO_REVIEW=1` → exit 2 (BLOCK Stop)

Evită deadlock-ul: hash diferit = reviewer needs rerun.

**BE/FE implementer split**: `backend-implementer.md` + `frontend-implementer.md` ca subagents dedicate, DOAR pentru sprint-uri >500 LOC, multi-track, sau high-risk cross-surface (e-Factura, fiscal hardware). Architect rămâne acoperit de Plan subagent built-in.

Implementăm Faza 2 DOAR dacă advisory Faza 1 se dovedește insuficient (>30% nudge ignored pe risky → blocking necesar).

## Verification matrix (status la commit)

| Step | Mod | Status |
|---|---|---|
| 1. Hook trivial silent | live | ✅ |
| 2. Hook non-trivial nudge | live (1304 lines diff = openapi.json regen) | ✅ |
| 3. Hook risky path detection | synthetic mock (V*.sql) | ✅ |
| 4. Hook $CLAUDE_AUTO_REVIEW verbosity | n/a (no risky in current real diff) | deferred |
| 5. Reviewer subagent direct invoke | RESTART REQUIRED | pending |
| 6. Test-runner subagent direct invoke | RESTART REQUIRED | pending |
| 7. .gitignore filter (runtime vs template) | `git check-ignore -v` | ✅ |
| 8. settings.json JSON valid + 8 hooks | live | ✅ |

## Git anchors

- **Meta-repo** `D:\reservation system\` (NEW, this work): `3d704cb chore: bootstrap Claude Code config meta-repo` (root commit, branch `main`, 46 files / 5193 insertions)
- **Reservation-System repo** `D:\reservation system\Reservation-System\`: `303e6018 chore(agent-loop): scaffold per-task scratchpad templates` (6 files / 64 insertions, branch `feature/recovery-02-fastops-1to1-ui-parity-port`)

Niciun push automat. Meta-repo n-are remote configured.

## Sanitize before re-adding deploy/SKILL.md

`deploy/SKILL.md` line 123 conține:
```
"email":"admin@reservation.local","password":"35ad565068aa43a28bd4f996"
```

Înainte de a-l re-adăuga în meta-repo:
1. Înlocuiește credential-ul literal cu `<ADMIN_SMOKE_PASSWORD>` placeholder
2. Adaugă instrucțiune în skill: "Read password from `.env.local` `ADMIN_SMOKE_PASSWORD` var"
3. Mută credential-ul real în `.claude/settings.local.json` (deja gitignored)
4. Remove `.claude/skills/deploy/SKILL.md` din `.gitignore`
5. `git add` + commit

## Cross-refs

- [[Reservation-System]] — proiectul principal
- [[Claude session boundary]] — cum lucrez cu vault între sesiuni
- [[Git workflow]] — workflow git pentru vault sync
- Repo paths:
  - Meta: `D:\reservation system\` (Windows) / `~/reservation-system-meta` (alt laptop, TBD)
  - Product: `D:\reservation system\Reservation-System\` (Windows) / `C:\Personal\Reservation-Systems\Reservation-System\` (per Reservation-System.md project note)
