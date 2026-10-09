# Stedsinfo – prosjektnotater for Claude Code

Svar alltid på norsk (bokmål). Eieren, Stein Arne, skriver norsk og er ikke utvikler: forklar enkelt og gi korte steg.

## Hva appen er
Personlig iPhone-PWA «Stedsinfo». Viser info om posisjonen du står på (Norge): adresse, kommune, fylke, gnr/bnr (Kartverket), verneområder (Miljødirektoratet/Naturbase), kulturminner (Riksantikvaren/Askeladden), og lenker til dekningskart og «Se eiendom». Eier og signalstyrke finnes ikke som åpne data.
Valget øverst: «Adresse» (søk på gate/nr/postnummer eller sted via Geonorge) og «Min posisjon» (GPS).
Søsterapp til Ruteinfo (egen mappe: `C:\Users\stein\Desktop\Ruteinfo-opplasting`, repo `madmoose56/Ruteinfo`).

## Filer (alt ligger i rota)
- `index.html` – hele appen i én fil (HTML+CSS+JS, ingen byggesteg).
- `sw.js` – service worker, nettverk-først. **Øk `CACHE` (`stedsinfo-vN`) ved hver utgivelse** (nå: v14).
- `manifest.webmanifest`, `icon-180.png`, `icon-512.png`, `README.md`.

## Kart
- Kartverket topo som bunn. Lag med avkrysning: kulturminner, verneområde, naturreservat, turstier. Lag uten treff innen 1 km skjules.
- «Vis liste» under kartet viser treff sortert på avstand; trykk på ett for å se det på kartet.
- «Åpne i full størrelse» gir fullskjerm. Radiusknapp bytter mellom 100 m og 1 km.

## Arbeidsmåte
- Må ligge på HTTPS (GPS krever det), f.eks. GitHub Pages: https://madmoose56.github.io/Stedsinfo/ (repo `madmoose56/Stedsinfo`, gren `main`). Claude committer og pusher selv.
- Test annen posisjon ved å legge `#59.9139,10.7522` på slutten av adressen.
- Kjør `node --check` på uttrukket JS etter endringer. Ingen Safari her: si tydelig hva som er testet og hva som bare kan bekreftes på iPhone.
- Skriv filer med Edit/Write (UTF-8), ikke PowerShell `Set-Content`: det ødelegger æøå.
- Be Stein Arne åpne siden i en privat Safari-fane hvis han ser gammel versjon.
