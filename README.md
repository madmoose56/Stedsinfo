# Stedsinfo

PWA for iPhone som viser informasjon om posisjonen du står på (Norge):

- adresse, kommune, fylke og gårds-/bruksnummer (Kartverket)
- verneområder: du står i / innen 100 m / innen 1 km (Miljødirektoratet, Naturbase)
- kulturminner: innen 100 m og 100 m til 1 km, med avstand (Riksantikvaren, Askeladden)
- lenker til dekningskart (Nkom, Telenor, Telia, Ice) og til «Se eiendom» for eierinfo

Eier og signalstyrke er ikke tilgjengelig som åpne data, se appen for detaljer.

## Filer

| Fil | Innhold |
| --- | --- |
| `index.html` | hele appen (HTML, CSS og JS) |
| `manifest.webmanifest` | PWA-manifest |
| `sw.js` | service worker (cacher bare app-skallet) |
| `icon-180.png`, `icon-512.png` | ikoner |

## Kjøre

Må ligge på HTTPS (GPS krever det), for eksempel GitHub Pages. På iPhone: åpne i Safari,
Del, «Legg til på Hjem-skjerm». For å teste en annen posisjon: legg til `#59.9139,10.7522`
etter adressen.

## Versjoner

Bytt `CACHE` i `sw.js` (for eksempel `stedsinfo-v2`) når du publiserer en ny versjon, så
cachen på telefonen byttes ut.
