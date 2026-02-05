
# Fasilitering av DDD Event Storming for Syntetisk Testdata

Denne markdown-filen beskriver hvordan man fasiliterer en komplett Domain-Driven Design (DDD) Event Storming-workshop for å modellere og designe **syntetisk testdata** basert på kilder fra:
- FREG (Folkeregisteret)
- Persontjenesten
- Helsepersonellregisteret (HPR)
- Fastlegeordningen (HELFO)
- Enhetsregisteret (BRREG)

Målet er å skape en helhetlig forståelse av domenet, modellere hendelser og prosesser, etablere bounded contexts, og definere en datagenereringsspesifikasjon for realistisk, konsistent og personverntrygg syntetisk testdata.

## Eksempel
---

## 1. Forarbeid

### Deltakere og roller
- Fasilitator
- Domeneeksperter (FREG, HPR, HELFO, BRREG)
- Dataarkitekt / tekniske ressurser
- Jurist/DPO
- Produktansvarlig

### Avklaringer før workshopen
- Hva er "Persontjenesten" i deres kontekst?
- Hvilke prosesser skal syntetisk data dekke?
- Ikke-funksjonelle krav (volum, kvalitet, sikkerhet)
- Juridiske føringer (GDPR, tilgangsstyring)
- Testmiljøer og API-er

### Praktiske forberedelser
- 1,5 dag workshop (1 dag Big Picture + 0,5 dag Design Level)
- Verktøy: Miro/Mural eller fysisk vegg
- Fargekoder: Orange = Events, Blå = Commands, Gul = Begreper, Grønn = Policies, Lilla = Aggregates
- Scenarier og testcases for syntetisk data

---

## 2. Dag 1: Big Picture Event Storming

### 2.1 Kickoff
- Formål: realisere syntetiske datasett med høy konsistens og referanseintegritet.
- Husregler: alt på tavla, lav terskel, ingen detaljmodellering tidlig.

### 2.2 Ubiquitous Language
Definer sentrale begreper som:
- Person (fra FREG)
- Helsepersonell, autorisasjon (fra HPR)
- Fastlege, hjemmel, listetilknytning (HELFO)
- Organisasjon, underenhet (BRREG)
- Skjerming, verge

### 2.3 Domain Events
Eksempler per domene:

#### FREG
- Person Registrert i FREG
- Adresse Endret i FREG
- Sivilstand Endret
- Dødsfall Registrert

#### HPR
- Helsepersonell Autorisert
- Autorisasjon Trukket
- Spesialitet Oppdatert

#### Fastlegeordningen
- Fastlegehjemmel Opprettet
- Innbygger Tildelt Fastlege
- Innbygger Byttet Fastlege
- Kapasitet Oppdatert

#### Enhetsregisteret
- Organisasjon Registrert
- Organisasjon Endret
- Organisasjon Avviklet/Fusjonert

### 2.4 Systemer og kilder
Koble hendelser til:
- FREG
- Persontjenesten
- HPR
- HELFO Fastlegeordning
- Enhetsregisteret

### 2.5 Commands og Policies
Eksempler:
- **Command:** Tildel Fastlege til Innbygger
- **Command:** Synkroniser Persondata
- **Policy:** Adresse Endret → vurder bytterett
- **Policy:** HPR-autorisasjon utløper → sperr hjemmel

### 2.6 Aggregates
- Person
- Helsepersonell
- Fastlegehjemmel
- InnbyggerFastlegeRelasjon
- Organisasjon

### 2.7 Bounded Contexts
Forslag:
- Person Context
- Fastlege Context
- HPR Context
- Organisasjon Context
- Integrasjons/Sync Context
- Sikkerhet & Tilgang Context

---

## 3. Dag 2: Design Level

### 3.1 Kritiske prosessflyter
Eksempler:
- Nyfødt → registrert i FREG → tildelt fastlege
- Flytting over kommunegrense
- Lege mister autorisasjon (HPR) → re-tildeling av innbyggere
- Fusjon av praksis (Enhetsregisteret)

### 3.2 Datakontrakter

#### Person
- syntetisk_fnr / syntetisk_dnr
- navn, kjønn, fødselsdato
- adresse med kommune
- status (bosatt, død, utvandret)
- skjermingsnivå
- verge

#### HPR
- syntetisk_hprnr
- profesjon
- autorisasjon_status
- gyldig_fra/til
- spesialiteter

#### Fastlege
- hjemmel_id
- lege_hprnr
- orgnr_praksis
- kapasitet (total/ledig)
- innbygger_fnr (listetilknytning)

#### Enhetsregisteret
- syntetisk_orgnr (MOD11)
- navn
- organisasjonsform
- adresse
- underenheter

### 3.3 Regler for syntetisering
- Kun syntetiske ID-er (ikke pseudonymisering)
- Realistiske fordelinger
- Konsistens: adresse → kommune → fastlegekrets
- Autorisasjon må være gyldig for aktiv fastlegehjemmel
- Generering av tidsserier for hendelser

### 3.4 Pipeline for syntetisk data
1. Generér organisasjoner og praksiser
2. Generér helsepersonell (HPR)
3. Generér personer (FREG)
4. Knytt personer til fastleger basert på kommune
5. Generér hendelser over tid
6. Materialiser tabeller og/eller event-logg

### 3.5 Akseptansekriterier
- 100% syntetiske ID-er
- Gyldige MOD11-verdier
- Konsistente relasjoner
- Hendelser re-spilles deterministisk
- Edge cases representert

---

## 4. Artefakter
- Event-kart
- Ubiquitous Language-ordbok
- Context Map
- Policy/Command/Event-matrise
- Datakontrakter (JSON Schema / OpenAPI)
- Synthetic Data Specification
- Testscenarier (Gherkin)
- Personvernnotat

---

## 5. Eksempel på YAML-regler
```yaml
dnr:
  share: 0.05
fnr:
  format: MOD11
orgnr:
  format: MOD11
hprnr:
  length: 7
skap_fordeling:
  skjermet: 1%
  verge: 2%
```

---

## 6. Dagsplan

### Dag 1
- 09:00–09:30: Intro
- 09:30–11:00: Event-storming (hendelser)
- 11:15–12:00: Policies og commands
- 13:00–15:30: Aggregates og bounded contexts

### Dag 2
- 09:00–10:30: Prosessflyter
- 10:45–12:00: Datakontrakter
- 13:00–15:00: Synthetic Data Spec

---

## 7. Oppsummering
Denne filen kan brukes som mal og som presentasjonsdokument for en DDD Event Storming-prosess knyttet til syntetisk testdata med avhengigheter til FREG, HPR, Fastlegeordningen og Enhetsregisteret.
