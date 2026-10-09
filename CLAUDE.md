# Stedsinfo – prosjektnotater for Claude Code

Svar alltid på norsk (bokmål). Eieren, Stein Arne, skriver norsk og er ikke utvikler: forklar enkelt og gi korte steg.

## Hva appen er
Personlig iPhone-PWA «Stedsinfo». Viser info om posisjonen du står på (Norge): adresse, kommune, fylke, gnr/bnr (Kartverket), verneområder (Miljødirektoratet/Naturbase), kulturminner (Riksantikvaren/Askeladden), og lenker til dekningskart og «Se eiendom». Eier og signalstyrke finnes ikke som åpne data.
Valget øverst: «Min posisjon» (GPS, til venstre, valgt automatisk ved oppstart) og «Velg adresse» (til høyre; søk på gate/nr/postnummer eller sted via Geonorge).
Søsterapp til Ruteinfo (egen mappe: `C:\Users\stein\Desktop\Ruteinfo-opplasting`, repo `madmoose56/Ruteinfo`).

## Filer (alt ligger i rota)
- `index.html` – hele appen i én fil (HTML+CSS+JS, ingen byggesteg).
- `sw.js` – service worker, nettverk-først. **Øk `CACHE` (`stedsinfo-vN`) ved hver utgivelse** (nå: v25).
- `manifest.webmanifest`, `icon-180.png`, `icon-512.png`, `README.md`.

## Kart
- «Åpne eiendomskart» i Eiendom-kortet åpner et eget fullskjermkart (matrikkelkart med teiger og gnr/bnr-tall, info nede til venstre, «‹ Tilbake»). Starter med samme radius som kartet nederst, med de to andre radiusene som knapper.
- Kartverket topo som bunn. Lag med avkrysning: kulturminner, verneområde, naturreservat (turstier er fjernet). Lag uten treff innen 1 km skjules.
- Kulturminner: de 50 nærmeste innen 1 km hentes; kortet viser 3 + «Vis flere». Hvert treff har nummer K1, K2 … (etter avstand), vist i kortet, i kartlisten og som merke på kartet (bare når laget er avkrysset og treffet er innenfor valgt radius). Kartradius 100 m / 500 m / 1 km: to knapper øverst til høyre på kartet viser de to radiusene som ikke er valgt. Riksantikvarens API (api.ra.no) tar med oppføringer uten geometri fra hele landet, så spørringen må avgrenses med `kommune=` (kommunene rundt posisjonen) i tillegg til `bbox`.
- Verneområder og naturreservat tegnes som skraverte polygoner (grønn/lilla) fra Miljødirektoratets data (alle innen 1 km), ikke som WMS-lag. Natur-kortet: «Der du står» + «Innen 1 km» med avstand bak navnet.
- Liten knapp «full størrelse» nede i høyre hjørne av kartet gir fullskjerm («‹ Tilbake» for å lukke). «Vis liste» under kartet er fjernet.

## Arbeidsmåte
- Må ligge på HTTPS (GPS krever det), f.eks. GitHub Pages: https://madmoose56.github.io/Stedsinfo/ (repo `madmoose56/Stedsinfo`, gren `main`). Claude committer og pusher selv.
- Test annen posisjon ved å legge `#59.9139,10.7522` på slutten av adressen.
- Kjør `node --check` på uttrukket JS etter endringer. Ingen Safari her: si tydelig hva som er testet og hva som bare kan bekreftes på iPhone.
- Skriv filer med Edit/Write (UTF-8), ikke PowerShell `Set-Content`: det ødelegger æøå.
- Be Stein Arne åpne siden i en privat Safari-fane hvis han ser gammel versjon.
