---
name: kraftprosjekt-loop
description: Kjør én iterasjon av overvåkningsloopen for europeiske kraftprosjekter og reguleringer — oppdater modenhetsgrad på alle prosjekter i masterlisten, kjør inkrementell discovery av nye prosjekter, oppdater kildegrafen og skriv beslutningsrapport. Bruk når brukeren ber om å kjøre kraftprosjektloopen eller oppdatere kraftprosjektkartet.
---

# Kraftprosjektloop — én iterasjon

Du kjører én iterasjon av den agentiske overvåkningsloopen for europeiske
kraftprosjekter og reguleringer som påvirker Hafslund. All research delegeres
til subagenter med **model: sonnet** — bruk aldri andre modeller til research.

## Steg 1 — Les tilstand

Les `kraftprosjektkart/masterliste.json` og `kraftprosjektkart/kildegraf.json`.
Merk deg `sist_kjort`-datoen og segmentinndelingen i masterlisten.

## Steg 2 — Spawn agenter (parallelt, alle med model: sonnet)

Send alle i samme melding så de kjører samtidig, `run_in_background: true`.
Bruk subagent_type `kraftprosjekt-forsker` (fallback: general-purpose med
model: sonnet og agentinstruksene limt inn i prompten).

1. **Én agent per segment i masterlisten** (havvind Norden, havvind Europa,
   landvind/sol Norden, vannkraft/pumpekraft, kjernekraft, nett/mellomlands-
   forbindelser, batterier/hydrogen/fleksibilitet, fjernvarme/CCS). Send med:
   segmentets prosjekter (id, navn, fase, modenhet), segmentets kilder fra
   kildegrafen, og `sist_kjort`-dato. Be om (a) faseendringer siden den datoen
   og (b) nye prosjekter som ikke står i listen.
2. **To reguleringsagenter**: én for EU-nivå, én for Norden (NO/SE/DK/FI).
   Send med kjente reguleringer fra masterlisten og be om statusendringer + nye
   forslag siden sist.
3. **Én discovery-agent på tvers**: let etter prosjekter/segmenter som helhets-
   bildet mangler (nye teknologier, nye auksjonsrunder, uventede aktører). Send
   med hele masterlistens id-liste som eksklusjonsliste.

## Steg 3 — Samle og oppdater tilstand

Når alle agentene er ferdige:

1. **Masterlisten**: oppdater fase/modenhet/siste_utvikling på eksisterende
   prosjekter, legg til nye (dedupliser på navn/id). Slett aldri prosjekter —
   kansellerte får `"fase": "kansellert"`, modenhet 0. Oppdater `sist_kjort`.
2. **Kildegrafen**: legg inn alle `nye_kilder` som noder under riktig segment.
   Behold eksisterende; marker døde lenker med `"status": "dod"`.
3. **Loggen**: skriv alle rå agentfunn til `kraftprosjektkart/logg/YYYY-MM-DD/`.

## Steg 4 — Rapport

Skriv `kraftprosjektkart/rapporter/YYYY-MM-DD-rapport.md` på norsk, som en
ekspert som skriver til beslutningstakere uten spesialkompetanse. Struktur:

1. Sammendrag — de 5 viktigste endringene siden sist og hva de betyr for Hafslund
2. Faseendringer per segment (hva har rykket frem/tilbake i modenhet)
3. Nye prosjekter oppdaget
4. Regulatorisk status og tidslinjer (EU + Norden)
5. Konsekvensbilde for Hafslund (priser, konkurranse, muligheter, trusler)
6. Datagap og anbefalinger

Publiser også en HTML-versjon som artifact (last `artifact-design`-skillen
først). Rapporten skal være pen, oversiktlig og brukbar for forretnings-
beslutninger: sammendrag øverst, modenhets-/relevansvisualisering, tabeller
per segment.

## Steg 5 — Levering

Lever rapportfilen til brukeren (SendUserFile) og artifact-lenken. Hvis et
e-postverktøy er tilkoblet, tilby å sende til erik.askheim.hjornevik@hafslund.no.
