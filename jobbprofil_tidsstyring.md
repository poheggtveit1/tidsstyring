# Funksjonell beskrivelse – Jobbprofil og Tidsstyring

---

## Definisjoner

| Begrep | Betydning |
|---|---|
| **Jobbprofil** | En modus som aktiverer arbeidsmodus for brukeren, med lagrede innstillinger for køpålogging, MBN SMS-varsling og visningsnummer |
| **Visningsnummer** | Nummeret som vises til innringer når brukeren ringer ut. «Mitt nummer» betyr brukerens faste, registrerte primærnummer i systemet |
| **Køstatus** | Kombinasjonen av pålogget/ikke-pålogget og MBN SMS-varsling på/av for en gitt kø |
| **MBN SMS-varsling** | Varslingstjeneste som sender pushvarsling til brukeren ved innkommende SMSer til en kø |
| **Tidsperiode** | Et definert tidsintervall på én eller flere ukedager med egne innstillinger for køpålogging og visningsnummer |

---

## 1. Jobbprofil

Jobbprofil er en modus som kan slås av og på manuelt av brukeren. Den lagrer og gjenoppretter køstatus og visningsnummer, slik at brukeren raskt kan gå inn og ut av arbeidsmodus.

### 1.1 Slå på Jobbprofil

Når Jobbprofil slås på:

- Logger brukeren på de køene som var pålogget siste gang Jobbprofil var aktiv.
- Setter MBN SMS-varsling på de køene som hadde dette aktivert siste gang Jobbprofil var aktiv.
- Setter visningsnummeret som var aktivt siste gang Jobbprofil var aktiv.
- Logger **ikke** av køer som brukeren allerede er pålogget. For slike køer beholdes eksisterende køstatus og MBN SMS-varslingsinnstilling – de overstyres ikke.

> **Første gangs bruk:** Dersom Jobbprofil aldri har vært aktivert tidligere, og det derfor ikke finnes noen lagret tilstand, pålogges ingen køer, MBN SMS-varsling aktiveres ikke, og visningsnummeret endres ikke. Brukeren må selv konfigurere ønsket oppsett.

### 1.2 Slå av Jobbprofil

Når Jobbprofil slås av:

- Lagrer gjeldende køstatus (pålogget/ikke-pålogget og MBN SMS-varsling på/av per kø) og gjeldende visningsnummer, slik at dette kan gjenopprettes neste gang Jobbprofil slås på.
- Logger brukeren av alle køer.
- Slår av MBN SMS-varsling på alle køer.
- Tilbakestiller visningsnummer til brukerens eget nummer.

### 1.3 Manuelle endringer mens Jobbprofil er aktiv

Når Jobbprofil er aktiv, kan brukeren til enhver tid:

- Logge på og av enkeltkøer manuelt.
- Endre MBN SMS-varsling per kø.
- Endre visningsnummer.

Disse endringene gjelder umiddelbart. Dersom Tidsstyring er aktiv, gjelder manuelle endringer frem til neste planlagte hendelse i Tidsstyringen, hvor Tidsstyringen igjen tar kontroll.

### 1.4 Visuell status

Appen viser tydelig om Jobbprofil er aktiv eller inaktiv, inkludert antall køer brukeren er pålogget.

Statusteksten under Jobbprofil-bryteren varierer avhengig av tilstand:

| Tidsstyring | Jobbprofil | Statustekst |
|---|---|---|
| På | På | 🟢 Tidsstyrt: Logget på n køer |
| På | Av | 🟢 Tidsstyrt: Avlogget |
| Av | På | 🟢 Logget på n køer |
| Av | Av | Logget av |

---

## 2. Tidsstyring

Tidsstyring gjør det mulig å automatisere Jobbprofil-innstillingene i løpet av arbeidsdagen ved å definere tidsperioder med egne innstillinger per ukedag. Tidsstyringslogikken kjøres på server og er uavhengig av om appen er aktiv på enheten.

### 2.1 Tidsperioder

- Tidsperioder konfigureres per ukedag (mandag–søndag).
- Ukedager uten definerte tidsperioder er inaktive – Tidsstyringen utfører ingen handlinger disse dagene.
- Tidsperioder på samme dag kan ikke overlappe. Systemet skal hindre overlappende perioder ved konfigurasjon.

### 2.2 Definisjon av en tidsperiode

Hver tidsperiode har en starttid og en sluttid, og inneholder følgende innstillinger:

#### Visningsnummer
- Det valgte visningsnummeret settes ved starten av tidsperioden.
- Visningsnummeret tilbakestilles til brukerens eget nummer ved slutten av tidsperioden.

#### Køer
For hver kø i tidsperioden velges én av følgende innstillinger:

| Innstilling | Oppførsel ved start | Oppførsel ved slutt |
|---|---|---|
| **Aktiv** | Logger på kø og setter MBN SMS-varsling på | Logger av kø og slår av MBN SMS-varsling |
| **Ingen endring** | Ingen handling | Ingen handling |

- MBN SMS-varsling for køer satt til «Aktiv» er som standard på, men kan endres manuelt av brukeren i løpet av tidsperioden, uavhengig av standardinnstillingen.
- Køer som ikke er valgt i tidsperioden (dvs. «Ingen endring») beholder sin eksisterende køstatus gjennom hele perioden – Tidsstyringen gjør ingen endringer for disse.

### 2.3 Aktivering av jobbprofil

Brukeren velger hvordan Tidsstyringen aktiverer Jobbprofil:

| Modus | Oppførsel |
|---|---|
| **Automatisk** | Jobbprofilen slås på og av automatisk i henhold til definerte tidsperioder |
| **Logg på selv** | Brukeren varsles 10 minutter før arbeidsøkten starter og slutter, og logger selv på og av via varselet |

### 2.4 Jobbprofil og Tidsstyring

- Jobbprofil slås **på** ved starten av første tidsperiode for dagen, dersom den ikke allerede er aktiv.
- Jobbprofil slås **av** ved slutten av siste tidsperiode for dagen, dersom den ikke allerede er inaktiv.
- Dette sikrer at brukeren alltid logges av alle køer ved slutten av arbeidsdagen, og ikke ved eventuelle hull mellom tidsperioder i løpet av dagen.

### 2.5 Overganger mellom tilstøtende tidsperioder

Når to tidsperioder følger direkte etter hverandre (sluttid for periode A = starttid for periode B), gjelder følgende:

- En kø som er satt til «Aktiv» i begge perioder, beholdes pålogget gjennom overgangen – brukeren logges ikke av og på igjen.
- Dersom visningsnummeret er ulikt i de to periodene, endres visningsnummeret direkte til neste periodes nummer ved periodens start – det tilbakestilles ikke til brukerens eget nummer i mellom.
- En kø som er «Aktiv» i periode A, men «Ingen endring» i periode B, logges **av** ved slutten av periode A.

### 2.6 Samspill med manuelle endringer

Manuelle endringer brukeren gjør i løpet av en tidsperiode (køpålogging, MBN SMS-varsling, visningsnummer) gjelder frem til neste planlagte hendelse i Tidsstyringen, hvor Tidsstyringen igjen tar kontroll.

Dersom brukeren manuelt slår av Jobbprofil mens en tidsperiode er aktiv, avsluttes gjeldende tidsperiode. Neste planlagte tidsperiode vil slå på Jobbprofil igjen ved sin starttid som normalt.

### 2.7 Visuell status for Tidsstyring

Statusteksten under Tidsstyring-bryteren på forsiden varierer avhengig av tilstand:

| Tilstand | Statustekst |
|---|---|
| Tidsstyring på, innenfor aktiv periode | Aktiv til [sluttid] · Neste: [dag] [starttid] |
| Tidsstyring på, utenfor aktiv periode (neste periode er en annen dag) | Neste: [dag] [starttid] |
| Tidsstyring på, utenfor aktiv periode (neste periode starter i dag) | Neste: i dag [starttid] |
| Tidsstyring slått av manuelt | Av |

---

## 3. Konfigurasjon – brukergrensesnitt

### 3.1 Opprette ny tidsperiode

Når brukeren oppretter en ny tidsperiode som starter umiddelbart etter en eksisterende periode:

- Starttid settes som standard til sluttiden til forrige periode.
- Køinnstillinger og visningsnummer settes som standard til samme verdier som forrige periode, slik at brukeren kun trenger å angi det som skal endres.
- Standardverdi for sluttid er starttid + 4 timer.

Når brukeren oppretter en tidsperiode uten tilstøtende eksisterende periode:

- Starttid og sluttid settes til standardverdier (f.eks. 08:00–16:00).
- Alle køer settes til «Ingen endring» som standard.

### 3.2 Validering

- Systemet hindrer overlappende tidsperioder på samme dag.
- Sluttid må være senere enn starttid.
- Brukeren varsles med en tydelig feilmelding dersom validering feiler.

### 3.3 Onboarding – første gangs oppsett av Tidsstyring

Første gang brukeren aktiverer Tidsstyring, gjennomgår brukeren en veiviser med følgende steg:

1. **Arbeidstid** – Velg starttid, sluttid og ukedager for arbeidsøkten.
2. **Visningsnummer** – Velg hvilket nummer som skal vises i arbeidsøkten.
3. **Køer** – Velg hvilke køer som skal aktiveres i arbeidsøkten. MBN SMS-varsling settes automatisk på for valgte køer, og kan endres manuelt i etterkant. Ved slutten av tidsperioden logges valgte køer av, MBN SMS-varsling slås av og visningsnummer tilbakestilles. Køer som ikke er valgt vil være upåvirket.
4. **Aktivering av jobbprofil** – Velg mellom «Automatisk» (logges på og av uten å gjøre noe) og «Logg på selv» (varsles 10 minutter før perioden starter og slutter). Varsling kan slås av under innstillinger i etterkant.

Etter gjennomført veiviser er Tidsstyring aktiv og konfigurert. Brukeren kan endre alle innstillinger via «Endre tidsstyring».
