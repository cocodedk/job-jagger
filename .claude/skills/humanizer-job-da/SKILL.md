---
name: humanizer-job-da
description: Use when reviewing or rewriting a Danish job application (ansøgning) before sending. Run after drafting, before PDF generation. Catches cover-letter-specific AI patterns that humanizer-da misses.
---

# Humanizer: Dansk Jobansøgning

Specialiseret humanizer til jobansøgninger. Bygger oven på humanizer-da men fanger mønstre der kun optræder i ansøgninger.

## Benchmark

**twoday/frontend-developer (2026-03-20)** er det bedste validerede eksempel på Babaks stemme. Brug den som kalibrering:
- Åbner med konkret arbejde, ingen opvarmning
- Meningen bag tests siges direkte: "I write tests. That is what makes the code work."
- Gap håndteres ærligt + confident bridge + connector-sætning der lukker det
- Sætninger forbindes naturligt med og/men/fordi/så — ikke hakket op i stumper
- Ingen glatte overgange eller runde afslutninger

## Kør i denne rækkefølge

0. Sæt positiv stemme (se nedenfor) — tjek hvad der MANGLER, ikke kun hvad der skal fjernes
1. Kør generel humanizer-da (em dashes, " - " som pausemarkør, tretalsmønstre, reklamesprog)
2. Kør forbudte åbninger-tjek
3. Kør kompetencepåstands-tjek
4. Kør ansøgningsspecifikke mønster-tjek
5. Kør "Ville Babak sige dette?"-testen
6. Kør åbnings-testen: match + bevis + værdi?
7. Kør jeg/du-balancetjek
8. Kør endelig AI-tjek

---

## Step 0: Sæt positiv stemme

Humanizeren fjerner mønstre. Men steril, stemmeløs tekst er ligeså AI-agtig som det du fjerner. Inden du retter fejl, tjek om teksten mangler disse **positive elementer**:

### Korte kontante sætninger med holdning
En-to sætninger per ansøgning der siger noget direkte og opinioneret. Mønstret fra twoday:
> "I write tests. That is what makes the code work."

Dansk ækvivalent (Todai, godkendt):
> "Jeg tester alt: pytest, Jest, Playwright. Det er det der gør at det virker i produktion, ikke kun i demo."

Identificér det vigtigste faglige statement i ansøgningen og sørg for at det siges direkte. Ikke indpakket i en glat sætning.

### Sætningslængde-variation — men pas på over-fragmentering
Scan sætningslængder. Jævne lange sætninger kan brydes op — men **for mange korte sætninger med punktum i træk er ligeså AI-agtigt som for lange**. Naturligt dansk bruger bindeord (og, men, fordi, så, eller) til at forbinde tanker. Stumper som "Ikke eksperimenter. Ting der kører." er fine én gang, men må ikke blive mønstret for hele brevet.

**Tegn på over-fragmentering:**
- Tre eller flere sætninger under 8 ord på stribe
- Hvert led i en opremsning har sit eget punktum
- Teksten lyder telegrafisk eller afkortet

**Fix:** Saml med bindeord. "Ikke som eksperimenter, men som komponenter der kører i drift" slår "Ikke eksperimenter. Ting der kører." i løbende prosa.

### Gap-håndtering: twoday-modellen
Nævn gabet direkte, forbind bridge og konklusion med men/så. Ét to til tre sætninger, ikke seks korte stumper.

Godkendt eksempel (Todai, revideret):
> "Azure er ikke det cloud-miljø jeg kender bedst, da jeg primært arbejder med Google Cloud og DigitalOcean, men principper og DevOps-tankegangen er de samme på tværs af udbydere, så det er ikke noget der tager lang tid at sætte sig ind i."

### Konkrethed frem for glatte overgange
Teksten må godt hoppe og navne må stå som opremsning ("LangChain, Anthropic, OpenAI, ChromaDB") — men saml dem i en sætning frem for at give hvert navn sin egen linje med punktum.

---

---

## Forbudte åbninger

Disse screenes altid ud:

| Mønster | Hvorfor |
|---|---|
| "Jeg søger hermed stillingen som..." | Pladsspild. Alle ved du søger. |
| "Med interesse og motivation søger jeg..." | Floskler. Intet indhold. |
| "Det er med stor interesse, at jeg..." | Bureaukratisk, stift. |
| "Som erfaren [titel] med X års erfaring..." | Identitet uden bevis. |
| Generel brancheobservation som åbning | Ingen match, ingen bevis, ingen relevans. |
| "Jeres spændende virksomhed tiltrækker mig..." | Smiger. Tror ingen på. |

**Stærk åbning** = 1 nøglekrav fra opslaget + 1 konkret bevis + hvad de får.

**Undtagelse:** Hvis interview-svarene giver en stærkere personlig åbning, vinder den - men kun hvis bevis følger inden første afsnit slutter. Personlighed + match + bevis er idealet.

---

## Forbudte kompetencepåstande

Erstat altid med konkrete eksempler - eller fjern helt:

- "resultatorienteret teamplayer" → hvad opnåede du, med hvem?
- "mange bolde i luften" → hvor mange projekter/kunder, hvad var resultatet?
- "fleksibel og struktureret" → alle siger det, ingen tror det
- "stærk kommunikator" → vis det i teksten
- "trives i teams / selvstændig" → overflødigt uden bevis
- "bred teknisk baggrund" → nævn det konkrete
- "solid erfaring med X" → hvad, hvor, hvilket resultat?
- "drives af at skabe resultater" → alle siger det
- "naturlig forlængelse af min karriere" → banned
- "brænder for X" → vis det i stedet for at fortælle det

---

## Ansøgningsspecifikke AI-mønstre

### Checkbox-struktur
Teksten besvarer krav fra jobopslaget punkt for punkt i rækkefølge. Læses som en AI der har skannet opslaget. Omskriv til naturlig fortælling med konkrete eksempler.

### Motivationsafsnit uden substans
"Stillingen tiltaler mig fordi I arbejder med [parafrase af opslaget]." Brug Babaks faktiske interview-svar om motivation i stedet.

### Afslutningsklichéer
- "Jeg håber, at min ansøgning har vakt jeres interesse" → fjern
- "Jeg er til jeres rådighed" → for underdanig
- **Acceptabelt:** "Jeg er tilgængelig til samtale og kan starte med kort varsel."
- **Bedre:** Noget konkret og direkte - gerne en kort invitation frem for en underdanig håben.

### Alle afsnit samme længde
Ensartet afsnitslængde er et AI-signal. Variér bevidst: korte sætninger efter lange, et kort afsnit efter et langt.

---

## "Ville Babak sige dette?"-testen

Læs hvert afsnit højt. Spørg: "Ville Babak sige dette til én han mødte til kaffe?"

**Tegn på at teksten ikke er hans:**
- For velformuleret og glat
- Ingen kant eller holdning
- Alt er positivt ladet - ingen nuancer
- Ingen ærlighed om gaps
- Læses som en præsentation, ikke en person

**Babaks stemme - kalibrer mod interview-svarene:**
- Direkte og handlingsorienteret
- Konkurrencementalitet: stopper ikke før opgaven er færdig
- Automatiserer det der kan automatiseres - manuel arbejde er ikke interessant
- Tænker forud for sin tid - ser hvad der skal ændres
- Vedholdenhed som konkret kendetegn, ikke som buzzword
- Ærlig om gaps - ikke undskyldende, men faktisk

Når interview-svar foreligger: brug Babaks egne formuleringer direkte i teksten hvor det falder naturligt.

---

## Jeg/du-balancetjek

Tæl forekomster af "jeg" i ansøgningen:

- **Under 4:** For upersonlig - tilsæt stemme og ansvar
- **5-8:** Ideelt
- **Over 10:** For selvfokuseret - skift nogle til du/I/jer-vinkel

Eksempler på skift:
- "Jeg har erfaring med ISO 27001" → "ISO 27001 er noget I får fra dag ét"
- "Jeg kan levere X" → "Det betyder for jer, at X"

Men: Fjern ALDRIG "jeg" til det punkt hvor teksten lyder robotagtig. 45% af danske arbejdsgivere fravælger ansøgninger der lyder AI-genererede - og en upersonlig tekst er et klart signal.

---

## Åbnings-tjek

Stil disse to spørgsmål om åbningsafsnittet:

1. **Bevis:** Starter åbningen med hvad Babak har gjort - konkret, med kontekst og resultat?
2. **Match:** Lander anden sætning matchet til det virksomheden søger?

Begge skal være til stede inden første afsnit slutter. Hvis nej → omskriv åbningen.

**Godkendt eksempel (Novo Nordisk Senior Security Partner):**
> "I tre år har jeg siddet embedded som sikkerhedskonsulent hos GlobalConnect og Nuuday - begge NIS2-kritisk infrastruktur med sikkerhedsgodkendelse - og arbejdet direkte med programledere og afdelingschefer på tværs af ISO 27001, risikostyring og kontroller i stor skala. Det er præcis det I søger til Finance, Legal og Global Solutions."

Bevis i sætning ét. Match i sætning to. Ingen opvarmning.

**Forbudte åbningsmønstre:**
- Forklaring af hvad rollen kræver ("kræver andet end...") - fortæller dem om deres eget job
- Babaks identitet eller erfaring som åbning ("Som erfaren konsulent med 25 års...")
- To afsnit inden matchet er etableret

---

## Endelig AI-tjek

Spørg: "Hvad gør denne tekst så tydeligt AI-genereret?" - og besvar ærligt.

Typiske resterende tegn:
- Alle afsnit har omtrent samme længde
- Ingen sætninger kortere end 8 ord
- Alt er positivt ladet, ingen nuancer
- Ingen kant, ingen humor, ingen holdning
- Teksten kunne sendes til enhver virksomhed med minimal ændring

Fix det der stadig ser AI-genereret ud - og revidér.
