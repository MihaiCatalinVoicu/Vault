---
title: Demo video strategy
type: reference
tags:
  - reference/sales
  - reference/demo
  - reference/marketing
  - product/reservation-system
updated: 2026-05-16
---

# Demo video strategy — VenueHub

> Reference durabil pentru producerea demo video-urilor de pilot prospecting. Înlocuiește improvizația live cu pipeline determinist + AI voiceover.

**Sursă:** discuție 2026-05-16 (răspuns la T1 Cold outreach din [[2026-05-16 Pre-mortem Reservation-System]]).

---

## Verdict de plecare

> **Da, fă screen recording. Probabil cea mai bună mișcare acum.**
> Dar **NU face demo cu agent AI care dă click-uri live**. Aia e ruletă rusească cu mouse-ul. Folosește AI-ul să-ți **genereze un demo runner determinist**, NU să improvizeze în timp ce înregistrezi.

**Formula corectă:**

```
Playwright script → flow automat → recording curat → AI voiceover → edit scurt → link la oameni
```

---

## Stack recomandat

### 1. Click-urile: Playwright (NU agent live)

Playwright are `codegen` care înregistrează acțiunile tale și generează cod cu locatori stabili (role/text/test-id). Flow repetabil, NU bâjbâială umană.

```bash
npx playwright codegen http://localhost:3000
```

Faci o dată flow-ul manual imperfect, apoi Claude/Codex curăță scriptul.

**NU folosi agent să dea click-uri live.** Folosește-l să scrie:
- `tests/demo/venuehub-owner-demo.spec.ts`
- Să-l facă să ruleze frumos.

### 2. Recording: OBS sau Playwright video

| Variantă | Când o folosești | Verdict |
|---|---|---|
| **OBS Studio** | Demo polished, trimis la oameni | Recomandat |
| **Playwright video recording** | Generare automată rapidă | Bun pentru draft/test |

- OBS Studio free open-source pentru screen recording + live streaming
- Playwright `video` option în config — util pentru drafturi/demo-uri automate; pentru material final folosește OBS peste browser-ul rulat de Playwright

### 3. Voce: AI voiceover separat (NU live)

Text separat, audio generat după ce flow-ul e stabil. NU "vorbește" în timp real peste demo.

| Tool | Utilitate |
|---|---|
| **ElevenLabs** | Voiceover cel mai profesional, RO + EN |
| **Descript** | Editare video/audio "ca text" + captions + screen recorder |
| **CapCut** | Simplu, TTS + editare rapidă pentru primă versiune |

---

## 3 video-uri, NU unul

### Video 1 — Teaser rece: 60-90 secunde

Pentru oameni care **NU te cunosc**. Scop: captezi interesul, NU să explici tot.

| Timp | Ce arăți |
|---|---|
| 0-10s | Problema: rezervări multi-canal, haos operațional |
| 10-25s | Dashboard VenueHub |
| 25-45s | Rezervare externă intră în sistem |
| 45-65s | Alocare masă / waitlist / status |
| 65-80s | Hotel + spa ca extensie (foarte scurt) |
| 80-90s | CTA: "caut 2-3 locații pilot / parteneri" |

### Video 2 — Demo warm lead: 4-6 minute

Pentru cine a răspuns interesat. Arăți mai mult:
- Restaurant reservation flow
- Floor board
- POS/order flow
- Waitlist / no-show / recovery
- Hotel/spa overview (scurt)
- Owner dashboard
- Accounting/compliance roadmap — doar SCURT

### Video 3 — Demo tehnic: 8-12 minute

Doar pentru **investitor/partener tehnic**. Arăți:
- Arhitectura
- Canale externe (foundation real, NU pretenții overclaiming)
- Module
- Ce e gata
- Ce e roadmap
- Ce înseamnă pilot

---

## Anti-patterns (NU face)

### NU arăta tot

NU face tur de aplicație ca un ghid de muzeu nervos:

> *"Aici avem Settings, aici avem Users, aici avem Fiscal Config, aici avem 47 de taburi și un dropdown care probabil funcționează."*

Demo-ul trebuie să spună **o poveste**:

> **"Patronul primește rezervări din orice canal. VenueHub le transformă în operațiuni controlabile: masă, client, status, staff, POS, revenue, hotel/spa."**

### NU overclaiming integrări

NU pretinde integrare reală Ialoc/Google/OpenTable dacă e doar simulată. Investitorii și partenerii **simt bullshit-ul** — au radar. Marchează explicit:

- "External channel simulation"
- "Designed for external reservation channels"

NU "live integration with X".

### Per [[2026-05-16 Reservation-System pre-pilot readiness]]

Status real:
- ✅ Email ingestion + bulk import + autopilot foundation funcționale
- 🔄 Auto-rules engine MVP în plan (EXT-RULES-001)
- 🚫 Google Reserve / Booking.com direct API = NU în scope MVP

Demo trebuie să reflecteze ASTA, nu marketing fluff.

---

## Flow video 90 secunde — scenariu

### Scene 1: Dashboard (0-10s)

Arăți dashboard curat:
- Rezervări azi
- Mese ocupate
- Waitlist
- Risc de no-show
- Revenue protected
- Hotel/spa cards

**Voiceover:**
> *"VenueHub centralizează operațiunile pentru locații HoReCa care nu mai vor să depindă de un singur canal de rezervări."*

### Scene 2: Rezervare externă (10-25s)

Arăți o rezervare venită din:
- Ialoc placeholder
- Google placeholder
- Telefon/manual
- Website

**Marker on screen:** "External channel simulation"

### Scene 3: Floor board (25-45s)

Rezervarea apare pe floor board. Aloci masa. Status:
booked → arrived → seated → serving → paid → closed

### Scene 4: Waitlist / Revenue Shield (45-65s)

Client întârzie. Sistemul marchează at-risk. Apare waitlist candidate.

Asta e **sexy comercial**. NU "CRUD de rezervări" (farmecul unui formular ANAF).

### Scene 5: Hotel + Spa (65-80s)

Scurt:
- Guest profile
- Room reservation
- Spa appointment
- Restaurant reservation legate în același sistem

NU insista dacă modulele nu sunt polished. Per [[2026-05-16 Reservation-System pre-pilot readiness]], hotel/spa fast-ops parity = WIP.

### Scene 6: Owner view (80-90s)

Arăți:
- Venit
- Ocupare
- Rezervări salvate
- Canale
- Operațiuni azi

**Voiceover închidere:**
> *"Caut locații pilot și parteneri HoReCa pentru validare comercială."*

---

## Cum faci flow-ul cursiv

### Demo runner technical setup

```bash
npm run demo:venuehub
```

Care:
1. Pornește aplicația
2. Login automat
3. Folosește demo tenant
4. Injectează date demo
5. Rulează flow-ul
6. Face pauze controlate
7. Eventual înregistrează video

### Playwright pattern

```ts
await page.goto('/login');
await page.getByLabel('Email').fill('owner@demo.venuehub.ro');
await page.getByLabel('Password').fill('DemoPass123!');
await page.getByRole('button', { name: 'Login' }).click();
await page.goto('/admin/restaurant/ops');
await page.waitForTimeout(1200);
```

### Scene helpers (preferat pentru demo)

```ts
await scene('Dashboard overview', async () => {
  await page.goto('/admin/dashboard');
  await pause(1800);
});

await scene('External reservation arrives', async () => {
  await injectDemoReservation({ source: 'Google / Ialoc simulation' });
  await page.goto('/admin/reservations');
  await pause(2200);
});
```

### Demo cursor — fake cursor în DOM

Playwright NU dă cursor frumos ca click manual. Soluție: injectezi cursor vizual fake.

```ts
async function moveDemoCursorTo(locator) {
  const box = await locator.boundingBox();
  await page.evaluate(({ x, y }) => {
    window.__demoCursorMove?.(x, y);
  }, {
    x: box.x + box.width / 2,
    y: box.y + box.height / 2,
  });
  await page.waitForTimeout(400);
}
```

CSS overlay:

```css
.demo-cursor {
  position: fixed;
  width: 18px;
  height: 18px;
  border-radius: 999px;
  pointer-events: none;
  z-index: 999999;
  transform: translate(-50%, -50%);
  transition: top 300ms ease, left 300ms ease;
}
```

Dă impresie de demo natural, fără mâna ta tremurând peste UI ca un trader după FOMC.

---

## Prompt template pentru Claude/Codex

Pentru a genera demo runner-ul în repo (când ești gata):

```text
You are working in the Reservation-System repo.

Goal:
Create a deterministic demo recording flow for VenueHub that can be run
locally and recorded with OBS or Playwright video.

Do not implement new product features unless absolutely required for
demo stability.
Do not change business logic.
Do not expose real data.
Use a dedicated demo tenant and seeded demo data.

Create:
1. A Playwright demo runner:
   tests/demo/venuehub-owner-demo.spec.ts
2. A demo seed/reset script:
   scripts/demo/seed-venuehub-demo-data.ps1
   or npm script if better for the existing project structure.
3. A README:
   docs/DEMO_RECORDING_RUNBOOK.md

Demo flow:
- Login as demo owner/operator.
- Open owner dashboard.
- Show today's reservation/ops summary.
- Show reservation intake from multiple channels:
  - website
  - phone/manual
  - Google/Ialoc simulation
- Open restaurant floor board.
- Assign a reservation to a table.
- Move status through arrived/seated/serving/paid/closed if supported.
- Show waitlist / at-risk / recovery board if available.
- Show hotel overview briefly.
- Show spa/calendar overview briefly.
- End on owner dashboard with clear business KPIs.

Technical requirements:
- Use stable data-testid selectors where missing.
- Add only minimal test ids, no visual redesign.
- Use viewport 1440x900.
- Hide dev-only UI, console errors, noisy banners, and random timestamps
  where possible.
- Add a demo cursor overlay helper so the recording shows smooth cursor
  movement and click pulses.
- Add scene helpers with consistent pauses:
  scene(title, callback)
  pause(ms)
  clickWithDemoCursor(locator)
- The demo must be rerunnable 3 times without data corruption.
- Prefer deterministic IDs/names:
  "Demo Guest"
  "Ialoc Simulation"
  "Google Maps Simulation"
  "Table 7"
  "Spa Massage 60m"
  "Room 204"

Recording:
- Add npm scripts:
  "demo:venuehub": runs the Playwright demo headed
  "demo:venuehub:video": runs with video enabled
- Document OBS settings:
  1920x1080 or 1440x900 capture
  30fps
  browser window only
  no microphone
  no webcam

Done when:
- The demo flow runs start-to-finish without manual input.
- It produces either a Playwright video artifact or can be recorded
  cleanly through OBS.
- The README explains exact commands.
- The final flow is suitable for a 90-second narrated product demo.
```

---

## Naration script teaser — RO

```text
Restaurantele și locațiile HoReCa nu primesc rezervări dintr-un singur loc.

Vin din website, telefon, Google, platforme externe, walk-in sau mesaje
directe. Problema este că operațiunile rămân fragmentate.

VenueHub centralizează rezervările și le transformă în acțiuni clare:
masă alocată, client urmărit, status operațional, waitlist, risc de
no-show și imagine de ansamblu pentru owner.

Pentru locațiile hibride, același sistem poate lega restaurantul de
hotel, spa și calendar operațional.

Scopul nu este încă o aplicație de rezervări. Scopul este un control
panel complet pentru HoReCa.

Caut locații pilot și parteneri care vor să testeze un sistem mai
complet, construit pentru operațiuni reale, nu doar pentru listări
online.
```

**Tip:** pentru AI voice, fă-l puțin mai lent decât pare normal. Voiceover-urile AI devin obositoare când sunt prea rapide. Oamenii deja au atenția zdrobită de reels, nu le mai da narator cu anxietate.

---

## Outreach messages

### Mesaj cold pentru operatori HoReCa

```text
Salut,

Lucrez la VenueHub, un sistem operațional pentru restaurante, hoteluri
și spa-uri.

Am făcut un demo scurt de 90 secunde care arată ideea principală:
rezervări din mai multe canale transformate în operațiuni clare pentru
staff și owner.

Video:
[link]

Caut feedback de la oameni din HoReCa și 1-2 locații pilot unde să
testez flow-ul pe cazuri reale.

Mulțumesc,
Mihai
```

### Mesaj pentru investitori/parteneri

```text
Salut,

Am construit VenueHub, un control panel operațional pentru HoReCa:
restaurant, hotel, spa și backoffice.

Poziționarea este simplă: nu înlocuim neapărat canalele de rezervări
existente, ci centralizăm rezervările și operațiunile într-un singur
sistem.

Demo 90 sec:
[link]

Caut discuții cu operatori/parteneri/investitori interesați de
digitalizarea HoReCa pe zona operațională, nu doar booking.

Mulțumesc,
Mihai
```

---

## Pre-recording checklist

### Obligatoriu

- [ ] Date demo curate (NU date reale)
- [ ] Fără console/devtools deschise
- [ ] Fără localhost vizibil dacă posibil (folosește custom domain `demo.venuehub.local` cu hosts file?)
- [ ] Fără bugs vizibile
- [ ] Fără loading lung neașteptat
- [ ] Fără "404 but ignore it"
- [ ] Fără click-uri inutile
- [ ] Fără meniuri goale
- [ ] Fără feature-uri neterminate vizibile

### Vizual

- [ ] Browser la 90-100% zoom
- [ ] Viewport constant 1440x900
- [ ] Tab-uri puține (sau 1 single tab)
- [ ] Dark/light mode consistent
- [ ] Font lizibil
- [ ] Cursor/click highlight (demo cursor overlay)
- [ ] Captions scurte

### Comercial

- [ ] 90 secunde MAXIM pentru video 1 (teaser)
- [ ] O singură poveste
- [ ] Un singur CTA ("caut pilot")
- [ ] NU "uite câte module am"
- [ ] NU promiți integrare reală Ialoc/Google dacă e simulată

---

## Cum trimiți demo-ul

NU atașa video mare pe email. **Trimite link.**

Options upload:
- Loom (recomandat, are analytics view count)
- YouTube unlisted
- Vimeo private
- Google Drive (no analytics, less professional)

---

## Realitate (de la sfârșitul discuției originale)

> "Raiffeisen Business, SPV ANAF, Orange Business: bun, ai acoperit baza. Nu mai folosi birocrația ca formă social acceptabilă de procrastinare, că statul român deja a gamificat suferința destul.
>
> Următorul pas nu mai e "încă un cont, încă o setare, încă un formular".
>
> Următorul pas este:
> 1. demo video
> 2. listă de 30 persoane/contacte
> 3. trimis 10 mesaje/zi
> 4. urmărit răspunsuri
> 5. 3 call-uri
> 6. 1 pilot
>
> Acolo începe realitatea. **Codul a fost partea confortabilă**, chiar dacă a părut iad. **Sales-ul e partea care îți spune dacă iadul are clienți.**"

---

## Cross-refs

- [[2026-05-16 Reservation-System pre-pilot readiness]] — status real al feature-urilor
- [[2026-05-16 Pre-mortem Reservation-System]] — T1 Cold outreach existential risk
- [[Reservation-System]] — project file
