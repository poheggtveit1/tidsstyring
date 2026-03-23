# MBN App – Jobbprofil

Interaktiv HTML-mockup for jobbprofil- og tidsstyringsfunksjonalitet i MBN App. Prototypen simulerer en mobilappskjerm (iPhone 375×812) og er bygget med ren HTML, CSS og JavaScript — ingen byggsteg nødvendig.

## Skjermer

| Fil | Beskrivelse |
|-----|-------------|
| `index.html` | **Jobbprofil** — Hovedside med manuell på/av-toggle, visningsnummer og køliste |
| `tidsstyring.html` | **Tidsstyring – onboarding** — 5-stegs wizard for å sette opp automatisk jobbprofil |
| `tidsstyring-detaljer.html` | **Tidsstyring – detaljer** — Oversikt over aktive arbeidstider |
| `endre-arbeidstid.html` | **Endre arbeidstid** — Rediger tidspunkt, visningsnummer og køstatus |
| `ny-arbeidstid.html` | **Ny arbeidstid** — Opprett ny arbeidstidsperiode |
| `utenfor-arbeidstid.html` | **Utenfor arbeidstid** — Innstillinger som gjelder når ingen arbeidstid er aktiv |
| `tidspunkt-og-dager.html` | **Tidspunkt og dager** — Velg starttid, sluttid og hvilke dager |

## Kom i gang

Åpne direkte i nettleser:
```bash
open index.html
```

Eller kjør en lokal server:
```bash
python3 -m http.server 3000
# Åpne http://localhost:3000
```

## Funksjonalitet

- **Jobbprofil-toggle** — Manuell på/av, husker siste tilstand for køer og visningsnummer
- **Tidsstyring** — Automatisk eller manuell på/avlogging basert på arbeidstid
- **Visningsnummer** — Velg visningsnummer med valgfri «Kun for eksterne samtaler»
- **Køstatus** — Per-kø innstilling for Ingen endring / Logg på / Logg av
- **SMS-varsling** — Følger køpålogging automatisk, kan overstyres manuelt
- **Lokal persistering** — Innstillinger lagres i `localStorage`

## Teknologi

- Ren HTML5, CSS og JavaScript
- Ingen avhengigheter eller byggsteg
- Designtokens hentet fra Figma (MBN App design system)
- iPhone-mockup (375×812px) med iOS-lignende UI-mønstre
