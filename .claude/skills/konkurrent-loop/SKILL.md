---
name: konkurrent-loop
description: Kjør én iterasjon av konkurrentovervåkningsloopen — oppdater masterlisten inkrementelt, spawn individuelle Sonnet-researchagenter per konkurrent, oppdater kildegrafen og skriv rapport. Bruk når brukeren ber om å kjøre konkurrentloopen eller oppdatere konkurrentkartet.
---

# Konkurrentloop — én iterasjon

Du kjører én iterasjon av den agentiske overvåkningsloopen for Hafslunds
konkurrenter. All research delegeres til subagenter med **model: sonnet** —
bruk aldri andre modeller til research.

## Steg 1 — Les tilstand

Les `konkurrentkart/masterliste.json` og `konkurrentkart/kildegraf.json`.
Merk deg `sist_kjort`-datoen i masterlisten.

## Steg 2 — Spawn agenter (parallelt, alle med model: sonnet)

Send alle i samme melding så de kjører samtidig, `run_in_background: true`:

1. **Én agent per tier 1-konkurrent** (subagent_type: konkurrent-forsker).
   Send med: navn, domener, selskapets kjente kilder fra kildegrafen, og
   `sist_kjort`-dato. Be om alt nytt SIDEN den datoen.
2. **1–3 gruppeagenter for tier 2** (5–8 selskaper per agent, samme format,
   men grunnere: kun vesentlige nyheter og endringer).
3. **Én discovery-agent** (general-purpose, sonnet): let etter NYE konkurrenter
   som IKKE står i masterlisten — nye aktører, nyetableringer, utenlandske
   aktører som har gått inn i Norge, fusjoner/navneskifter. Send med dagens
   masterliste som eksklusjonsliste. Returnerer kandidater i samme JSON-format
   som masterlisten.
4. **Én Hafslund-baseline-agent** (sonnet): sjekk Hafslunds egen synlighet —
   SERP-posisjoner på kjernetermer (søk på "vannkraft", "fjernvarme Oslo",
   "energiselskap Norge", merkevaretermer), Hafslunds egne annonser i Google
   Ads Transparency Center og Meta Ad Library, og sammenlign med tier 1.
   NB: Google Keyword Planner og Google Search Console krever kontotilgang —
   hvis det ikke finnes et tilkoblet verktøy/MCP for dette, noter det som gap
   i stedet for å forsøke innlogging.

## Steg 3 — Samle og oppdater tilstand

Når alle agentene er ferdige:

1. **Masterlisten**: legg til nye konkurrenter fra discovery-agenten (dedupliser
   mot eksisterende, vurder tier selv). Fjern aldri selskaper uten grunn — marker
   heller `"status": "inaktiv"` ved fusjon/avvikling. Oppdater `sist_kjort`.
2. **Kildegrafen**: legg inn alle `nye_kilder` fra agentene som noder/kanter.
   Behold eksisterende kilder; marker døde lenker med `"status": "dod"`.
3. **Loggen**: skriv alle rå agentfunn til `konkurrentkart/logg/YYYY-MM-DD.json`.

## Steg 4 — Rapport

Skriv `konkurrentkart/rapporter/YYYY-MM-DD-rapport.md` på norsk, som en ekspert
som skriver til en prosjektleder uten spesialkompetanse: alle detaljer skal med,
men forklart forståelig. Struktur:

1. Sammendrag (de 3–5 viktigste utviklingene)
2. Nytt per tier 1-konkurrent
3. Tier 2 — vesentlige endringer
4. Nye konkurrenter oppdaget / endringer i masterlisten
5. Annonsebildet (Google/Meta ad libraries)
6. Hafslund-baseline og relativ posisjon
7. Datagap og anbefalinger

## Steg 5 — Levering

Hvis et e-postverktøy er tilkoblet (Gmail/Outlook MCP): send rapporten til
erik.askheim.hjornevik@hafslund.no etter reglene for utgående meldinger.
Hvis ikke: lever rapportfilen til brukeren (SendUserFile) og publiser gjerne
som artifact, og nevn at e-postintegrasjon mangler.
