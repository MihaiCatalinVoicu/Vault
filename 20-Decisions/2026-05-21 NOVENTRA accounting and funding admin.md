---
title: NOVENTRA accounting and funding admin
type: decision
date: 2026-05-21
status: active
project: "[[Reservation-System]]"
tags:
  - decision/reservation-system
  - company/noventra
  - accounting
  - funding
  - anaf
---

# 2026-05-21 — NOVENTRA accounting and funding admin

## Context

NOVENTRA SYSTEMS S.R.L. pregateste:

- contract de contabilitate curenta;
- posibila aplicare la fonduri europene;
- paid pilot billing pentru VenueHub;
- viitor flux e-Factura/ANAF controlat, dar inca fara proof real ANAF TEST.

## Incadrare firma pentru fonduri

Incadrarea probabila pentru fonduri europene: **microintreprindere IMM**.

Motiv:

- firma este noua / foarte mica;
- sub 10 salariati;
- sub pragurile de 2 milioane EUR cifra de afaceri / active;
- nu exista indicii de intreprinderi partenere/legate care sa trebuiasca agregate.

Nota: aceasta este incadrarea IMM pentru fonduri, nu neaparat acelasi lucru cu regimul fiscal de microintreprindere din Codul Fiscal.

## ProjectXpert / formular fonduri

Pentru formularul de interes fonduri:

- tip investitie recomandat: **dotare spatiu prestari servicii si investitii conexe**, nu constructie/modernizare spatiu.
- CAEN recomandat: **6201 - Activitati de realizare a soft-ului la comanda**.
- CAEN alternative daca este cazul:
  - 6202 - consultanta IT;
  - 6311 - prelucrarea datelor / administrarea paginilor web si activitati conexe.

Text propus pentru cele trei idei principale:

```text
1. Dezvoltarea unei platforme software SaaS pentru digitalizarea operatiunilor HoReCa: rezervari, POS, gestiune operationala si facturare/e-Factura.

2. Achizitia de echipamente IT, licente software, servicii cloud si instrumente necesare pentru dezvoltarea, testarea si operarea platformei.

3. Cresterea capacitatii firmei de a furniza servicii IT prin automatizare, securizare, integrare cu sisteme fiscale si pregatirea lansarii comerciale catre restaurante/hoteluri.
```

Documente cerute in formular:

- bilant ultim exercitiu financiar: probabil **nu exista inca**, firma fiind nou-infiintata;
- certificat constatator ONRC: se poate obtine, trebuie nu mai vechi de 30 zile.

## Contract contabilitate

Document verificat:

```text
C:\Users\mvoicu1\Downloads\Contract contabilitate 202339 NOVENTRA SYSTEMS.docx
```

Verdict practic: **acceptabil ca baza pentru contabilitate lunara**, dar necesita clarificari/corectii inainte de semnare.

Ce este ok:

- prestatorul este firma de expertiza contabila / CECCAR;
- pret de baza rezonabil: 400 lei/luna pana la 25 acte, neplatitor TVA;
- include contabilitate financiara, balanta, jurnale, declaratii, bilant;
- include payroll daca apar salariati, cu tarif separat;
- preaviz 30 zile.

Corectii / clarificari obligatorii:

1. Date NOVENTRA incomplete sau gresite:
   - cont bancar si banca lipsesc;
   - email client este doar `@`;
   - typo in semnatura: `NOVENTRA SYSTEMSS.R.L.`.
2. Clarificare scrisa pentru e-Factura/SPV:
   - daca abonamentul include descarcare facturi din SPV/e-Factura;
   - daca include emitere/trimitere 1-2 facturi/luna;
   - ce se intampla peste 2 facturi emise/luna.
3. Clauza de neangajare este prea dura:
   - 5 ani;
   - penalitate 10.000 EUR;
   - recomandare: eliminare sau reducere la 12 luni.
4. Penalitati de intarziere agresive:
   - 0,5%/zi;
   - pot depasi debitul principal;
   - recomandare: plafon la debitul principal.
5. Raspunderea prestatorului este limitata la doua onorarii lunare:
   - aproximativ 800 lei la abonamentul actual;
   - recomandare: ceruta dovada asigurare profesionala si/sau plafon mai realist.

Ce NU include explicit:

- registru de casa;
- ordine de plata / operatiuni bancare;
- arhivare documente;
- facturare ca serviciu general;
- SPV/e-Factura explicit formulat.

## Estimare facturi lunare

Pentru faza actuala:

- facturi emise de NOVENTRA: realist **0-2/luna**;
- fara client platitor: 0;
- un pilot platit: 1;
- doi clienti/piloti sau avans + rest plata: 2.

Daca contabilul include descarcare facturi si trimitere 1-2 facturi/luna, este suficient pentru etapa actuala.

Documente primite pot fi mai multe:

- factura contabil;
- banca/comisioane;
- domeniu/hosting/cloud;
- software subscriptions;
- echipamente;
- ads / Google / Meta;
- ONRC / certificate / servicii diverse.

Recomandare operationala:

1. In primele luni contabilul emite/trimite primele 1-2 facturi.
2. Dupa 3+ facturi/luna, NOVENTRA emite in platforma de facturare.
3. Contabilul verifica, descarca si inchide SPV/e-Factura.

Intrebare de trimis contabilului:

```text
In abonamentul de 400 lei sunt incluse descarcarea facturilor din SPV/e-Factura si emiterea/transmiterea a 1-2 facturi lunare? Daca depasesc 2 facturi emise, imi aratati platforma si eu le emit, iar voi doar verificati contabil?
```

## Boundary fiscal

VenueHub are tooling intern pentru invoice drafts si e-Factura readiness, dar pana la proof real ANAF:

- facturarea comerciala ramane prin contabil/tool extern/SPV;
- nu se pretinde ca VenueHub face legal invoice submission;
- nu se activeaza real upload/status/download fara release gate.

