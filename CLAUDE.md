# Stedsinfo – prosjektnotater for Claude Code

Svar alltid på norsk (bokmål). Eieren, Stein Arne, skriver norsk og er ikke utvikler: forklar enkelt og gi korte steg.

## Hva appen er
Personlig iPhone-PWA «Stedsinfo». Viser info om posisjonen du står på (Norge): adresse, kommune, fylke, gnr/bnr (Kartverket), verneområder (Miljødirektoratet/Naturbase), kulturminner (Riksantikvaren/Askeladden), og lenker til dekningskart og «Se eiendom». Eier og signalstyrke finnes ikke som åpne data.
Valget øverst: «Min posisjon» (GPS, til venstre, valgt automatisk ved oppstart) og «Velg adresse» (til høyre; søk på gate/nr/postnummer eller sted via Geonorge). De 10 siste valgte adressene lagres i localStorage (`stedsinfo-adresser`); skriver man første bokstav i Gate vises matchende forslag, og et trykk fyller Gate, Nr, Postnummer og Sted.
Søsterapp til Ruteinfo (egen mappe: `C:\Users\stein\Desktop\Ruteinfo-opplasting`, repo `madmoose56/Ruteinfo`).

## Filer (alt ligger i rota)
- `index.html` – hele appen i én fil (HTML+CSS+JS, ingen byggesteg).
- `sw.js` – service worker, nettverk-først. **Øk `CACHE` (`stedsinfo-vN`) ved hver utgivelse** (nå: v59).
- `manifest.webmanifest`, `icon-180.png`, `icon-512.png`, `README.md`.

## Plankart og reguleringsplan
- Planregister-lenken (arealplaner.no) er ikke med (fjernet etter ønske). Boksen «Kommunekart» (under Eier) har én knapp som åpner Kommunekart på posisjonen. Funn: finnes ikke som åpent landsdekkende kartlag; kommunenes planregister ligger på arealplaner.no, og Kommunekart kan åpnes på posisjon med `funksjon=vispunkt&x=lat&y=lon`.

## Natur-kortet og høyde
- Kortet «Natur» har tre deler fra Miljødirektoratets ArcGIS-tjenester (kart.miljodirektoratet.no): Verneområder (laget vern), Naturtyper (naturtyper_hb13, lag 0; kodeliste for naturtype er lagt inn i koden) og Friluftslivsområder (friluftsliv_kartlagt, lag 1). Alt innen 1 km, 3 nærmeste + «Vis flere» (maks 50).
- Høyde over havet i Sted-kortet kommer fra Kartverkets høyde-API (ws.geonorge.no/hoydedata/v1/punkt).

## Nye kort (v47)
- **Arter i nærheten:** funn innen 500 m siste 5 år fra GBIF (api.gbif.org, bbox) og Artskart/Artsdatabanken (artskart.artsdatabanken.no/publicapi, `filter.wktPolygon`; trege svar, ofte 10–15 s; bbox-filteret virker ikke). Slått sammen, dublettfjernet, gruppert per art (rødlistede først), `<details>` med funn.
- **Geologi, løsmasser og radon:** NGU WMS GetFeatureInfo (GML) i punktet: LosmasserWMS2/Losmasse_flate, BerggrunnWMS3/Berggrunn_lokal_hovedbergarter_fullzoom, RadonWMS2/Radon_aktsomhet.
- **Kollektivtransport:** Entur Journey Planner v3 (api.entur.io/journey-planner/v3/graphql) med header `ET-Client-Name: madmoose56-stedsinfo`; nærmeste holdeplasser innen 500 m med sanntidsavganger og rullestoltilgang (Quay.wheelchairAccessible; Stop Place Register-API-et er stengt for oss). «Planlegg reise»-lenken (entur.no/reiseresultater?startLat&startLon) ble fjernet etter ønske.

## Fjernet
- Boksen «Sjekk mobildekning» (lenker til Telenor/Telia/Ice) er fjernet etter ønske; det finnes ingen åpen tjeneste for dekning i et punkt.

## Nummererte punkter på kartet
- Avkrysninger for Holdeplasser (H1…), Arter (A1…), Naturtyper (N1…) og Friluftsliv (F1…), i tillegg til Kulturminner (K1…). Samme nummer står i kortene. Vises bare når laget har treff, er avkrysset og punktet ligger i det synlige kartutsnittet (med litt margin; ikke bare innenfor radiussirkelen, slik at 100 m-visningen også får med hjørnene); navn ved zoom ≥ 16. Arter: én markør per art ved nærmeste funn. Polygoner (natur/friluft) får punkt ved nærmeste hjørne, eller midten av omrisset hvis man står i dem.

## «Kart»-knapp ved treff
- Hvert treff i Kollektivtransport, Natur (verneområder, naturtyper, friluftsliv), Arter og Kulturminner har en liten «Kart»-knapp (klasse `kartbtn`, funksjon `kartTilTreff`): åpner fullskjermkartet sentrert på treffet med radius 100 m og en gul ring rundt treffet.

## Kart
- Fullskjerm av nederste kart: starter på 100 m med radiusvalgene 100 m / 500 m / 1 km (som vanlig visning). Filtervalgene ligger i en meny som trekkes ut fra høyre side med håndtaket «Vis»; alle valg krysses av automatisk når fullskjermen åpnes (forrige avkrysning gjenopprettes ved «Tilbake»), og et trykk på kartet lukker menyen. Ved «‹ Tilbake» gjenopprettes forrige radius og utsnitt.
- Bunnkart-bytte (knapper nede til venstre, på samme linje som «full størrelse» som står til høyre): Topo (Kartverket topo), Flyfoto (Esri World Imagery; Kartverkets Norge-i-bilder-adresse virket ikke) og Bygninger (Kartverket gråtone + matrikkelens bygningslag). Valget huskes. Navn vises ved K-merkene og spisestedene når zoom ≥ 16.
- Valget «Spisesteder» (av som standard): restauranter, kafeer og hurtigmat innen 1 km fra OpenStreetMap via Overpass (hedged mellom flere servere), vist som gaffel/kniv-markører innenfor valgt radius. Skjules hvis ingen treff eller hvis Overpass ikke svarer.
- «Åpne eiendomskart» i Eiendom-kortet åpner et eget fullskjermkart (matrikkelkart med teiger og gnr/bnr-tall, info nede til venstre, «‹ Tilbake»). Åpnes alltid på 100 m, med de to andre radiusene (500 m, 1 km) som knapper.
- Kartverket topo som bunn. Lag med avkrysning: kulturminner, verneområde, naturreservat, turstier (Kartverkets friluftsruter, laget «Fotrute»; vanlig WMS-lag; rød stiplet versjon ble prøvd og fjernet igjen). Lag uten treff innen 1 km skjules.
- Kulturminner: de 50 nærmeste innen 2 km (søker først 1 km; er det færre enn 50 treff utvides til 2 km); kortet viser 2 + «Vis flere». Hvert treff har nummer K1, K2 … (etter avstand), vist i kortet, i kartlisten og som merke på kartet (bare når laget er avkrysset og treffet er innenfor valgt radius). Kartradius 100 m / 500 m / 1 km: to knapper øverst til høyre på kartet viser de to radiusene som ikke er valgt. Riksantikvarens API (api.ra.no) tar med oppføringer uten geometri fra hele landet, så spørringen må avgrenses med `kommune=` (kommunene rundt posisjonen) i tillegg til `bbox`.
- Verneområder og naturreservat tegnes som skraverte polygoner (grønn/lilla) fra Miljødirektoratets data (alle innen 1 km), ikke som WMS-lag. Natur-kortet: områder du står i (0 m) og «Innen 1 km», avstand bak navnet; ingen «Der du står»-overskrift (fungerer også for valgt adresse).
- Liten knapp «full størrelse» nede i høyre hjørne av kartet gir fullskjerm («‹ Tilbake» for å lukke). «Vis liste» under kartet er fjernet.

## Arbeidsmåte
- Må ligge på HTTPS (GPS krever det), f.eks. GitHub Pages: https://madmoose56.github.io/Stedsinfo/ (repo `madmoose56/Stedsinfo`, gren `main`). Claude committer og pusher selv.
- Test annen posisjon ved å legge `#59.9139,10.7522` på slutten av adressen.
- Kjør `node --check` på uttrukket JS etter endringer. Ingen Safari her: si tydelig hva som er testet og hva som bare kan bekreftes på iPhone.
- Skriv filer med Edit/Write (UTF-8), ikke PowerShell `Set-Content`: det ødelegger æøå.
- Be Stein Arne åpne siden i en privat Safari-fane hvis han ser gammel versjon.
