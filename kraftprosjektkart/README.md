# Kraftprosjektkart

Agentisk overvåkningsloop for europeiske kraftprosjekter og reguleringer som
påvirker Hafslund. Kjøres med `/kraftprosjekt-loop`.

## Struktur

- `masterliste.json` — alle kjente prosjekter og reguleringer, med fase og
  modenhetsgrad (0–5). Oppdateres inkrementelt av loopen: eksisterende
  prosjekter får ny fase/modenhet, nye prosjekter legges til, ingenting slettes.
- `kildegraf.json` — graf over segmenter → prosjekter → verifiserte kilder
  (prosjektsider, konsesjonsregistre, auksjonssider, TSO-planer). Hukommelse
  mellom kjøringer: hver researchagent får sitt segments kilder med seg, og
  nye kilder legges tilbake hit.
- `logg/YYYY-MM-DD/` — rå agentfunn per kjøring.
- `rapporter/` — datert beslutningsrapport per kjøring (md + html-artifact).

## Modenhetsskala

| Modenhet | Fase |
|---|---|
| 1 | Konsept / tidlig idé |
| 2 | Planlegging / utredning / konsesjonssøknad |
| 3 | Konsesjon gitt / auksjon vunnet |
| 4 | FID tatt / under bygging |
| 5 | I drift |
| 0 | Kansellert / skrinlagt |

## Avgrensning

Norden: alle prosjekter over ~50 MW. Europa ellers: over ~200 MW eller
strategisk viktige (mellomlandsforbindelser, first-of-a-kind, prisdrivende).
All research kjøres av Sonnet-subagenter (`kraftprosjekt-forsker`).
