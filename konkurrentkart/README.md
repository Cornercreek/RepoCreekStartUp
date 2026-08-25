# Konkurrentkart — agentisk overvåkningsloop

Dette systemet kartlegger og overvåker Hafslunds konkurrenter (kraftprodusenter,
fjernvarmeaktører og fornybar-utbyggere) med individuelle research-agenter.

## Filstruktur

| Fil | Innhold |
|---|---|
| `masterliste.json` | Masterlisten over alle konkurrenter, med tier, segmenter og domener. Oppdateres inkrementelt av loopen. |
| `kildegraf.json` | Graf over selskaper → domener → kilder (nyhetsrom, IR-sider, ad libraries m.m.). Agentene bruker denne som "hukommelse" mellom kjøringer, så de går rett til riktige kilder. |
| `logg/` | Rådata fra hver kjøring (én JSON-fil per kjøring med alle agentfunn). |
| `rapporter/` | Ferdige rapporter per kjøring, datert. |

## Slik kjører du loopen

Kjør skillen `/konkurrent-loop` i Claude Code fra repo-roten. Den:

1. Leser `masterliste.json` og `kildegraf.json`.
2. Spawner én Sonnet-researchagent per tier 1-konkurrent (+ grupperte agenter for tier 2)
   som finner alt nytt siden forrige kjøring, inkl. annonser i Google Ads Transparency
   Center og Meta Ad Library.
3. Kjører en egen "discovery"-agent som leter etter NYE konkurrenter som ikke står
   på listen (inkrementell oppdatering av masterlisten).
4. Oppdaterer kildegrafen med nye kilder som ble funnet.
5. Skriver rapport til `rapporter/` og leverer den.

For automatisk kjøring på intervall: `/loop /konkurrent-loop` eller sett opp en
scheduled task.

## Kjente begrensninger (krever tilgang/oppsett)

- **Google Keyword Planner** krever innlogget Google Ads-konto (API eller UI).
  Kan ikke nås uten at kontotilgang settes opp.
- **Google Search Console** krever eierskap/tilgang til Hafslund-propertyen.
  Baseline gjøres i mellomtiden med offentlige signaler (SERP-sjekker, ad libraries,
  offentlige SEO-data).
