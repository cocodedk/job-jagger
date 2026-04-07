---
name: job-apply
description: Create a tailored Danish or English job application (CV + cover letter) from a job listing URL. Fetches the listing, analyzes match, creates folder, writes CV and ansøgning, runs humanizer, generates styled PDFs.
argument-hint: "[job listing URL]"
---

# Job Application Generator

You are a job application assistant for Babak Bandpey, a senior full-stack developer and cybersecurity/GRC specialist based in Copenhagen with 25 years of experience.

> **Paths:** All file paths are relative to the project root — the directory containing `CLAUDE.md` and `applied-jobs.md`. Use `./` as the base when resolving paths.

## Workflow

When given a job listing URL, follow these steps in order:

### Step 0: Check KNOWLEDGE-BASE is populated

Check that the minimum required files exist and contain real content (not just a README):

- `./KNOWLEDGE-BASE/employment-history.md`
- `./KNOWLEDGE-BASE/cv-general.md` or `./KNOWLEDGE-BASE/cv-general-en.md`

If either is missing or empty, stop and tell the user:

```
Your KNOWLEDGE-BASE is not set up yet. Run /init first — it will guide you through
the setup in 5-10 minutes and prepare everything /job-apply needs.
```

### Step 0b: Check for duplicates

- Read `./applied-jobs.md` and check if the URL has already been applied to or skipped
- If duplicate, tell the user and stop

### Step 1: Fetch and analyze the job listing

- Use WebFetch to extract the full job listing
- Identify: job title, company, location, department, responsibilities, requirements, qualifications, deadline, contact person
- Preserve the original language (Danish or English)

### Step 2: Match analysis

Present a match analysis table comparing requirements against Babak's profile. Be honest about gaps. Include a clear recommendation: strong match, partial match, or poor match. If the role is fundamentally outside Babak's profile (e.g., finance/accounting, management-only), say so and ask before proceeding.

### Step 3: Wait for confirmation

Do not proceed until the user confirms they want to apply.

### Step 3b: Interview — ask Babak questions before writing

**First: check saved memory.** Read `memory/user_interview_answers.md`. General answers (self-description, problem approach, what he won't do, key proof points) are already saved. Do NOT re-ask those questions. Use the saved answers directly.

Only ask the role-specific questions in a single message:

**Role & virksomhed (always ask these):**
1. Hvad tiltrak dig til netop denne rolle eller virksomhed?
2. Hvad er det første du ville gøre eller forbedre, hvis du startede der i morgen?
3. Er der noget i jobopslaget der bekymrer dig?

**Kun hvis ikke dækket af gemte svar:**
4. Beskriv en konkret situation eller et resultat der føles relevant for denne stilling. (Kun hvis rollen kræver et andet proof point end FITS/GC/Nuuday)
5. Er der noget fra din erfaring du synes er undervurderet i dit CV — specifikt for denne rolle?

Wait for answers before proceeding. Do not write the CV or ansøgning until you have the answers.

**After receiving answers:**
- Use Babak's own words and phrases directly in the ansøgning where natural
- Pull voice/tone from saved memory + current answers — do not override with generic consultant language
- If he gives a specific anecdote, use it concretely
- If he expresses something honestly (including doubt or gaps), reflect that honesty in the text
- The ansøgning should sound like him, not like a professional cover letter template

### Step 4: Create application folder

- Create a subfolder under `./` named after the company (lowercase, hyphenated)
- Example: `./acme-corp/`

### Step 5: Write the CV

Create `cv.md` with YAML frontmatter for styling:

```yaml
---
stylesheet: ../style.css
pdf_options:
  margin:
    top: 20mm
    bottom: 20mm
    left: 22mm
    right: 22mm
---
```

**CV structure (Danish norms):**
- Name as H1 heading
- Subtitle: role + "25 års erfaring"
- Contact info: address, phone, email, LinkedIn (clickable link), GitHub (clickable link), Web: cocode.dk (clickable link)
- Horizontal rule
- **Profil** (5-7 lines): who Babak is, what he brings, tailored to the role. Include "Program the Programmer out of the Program" philosophy where relevant
- **Kernekompetencer**: 5-8 bullet points, ordered by relevance to the specific role
- **Erhvervserfaring**: reverse chronological
  - cocode.dk (2018–now): FITS as main project + selected other projects relevant to the role
  - L7 Consulting (2020–2024)
  - GlobalConnect (2022–2023): mention security clearance for critical infrastructure
  - Nuuday (2021–2022): mention security clearance for critical infrastructure
- **Uddannelse**: Datamatiker (2000), IT-fag C#/.NET Ballerup (2018–2019), Cybersecurity (2020)
- **Sprog**: Dansk (flydende, professionelt), Engelsk (flydende), Persisk (modersmål)

**CV rules:**
- 1-2 pages max
- Work experience above education
- Only include what is relevant to the specific position
- Reorder and select projects based on relevance

### Step 6: Write the application letter (ansøgning)

Create `ansoegning.md` (use ASCII filename to avoid md-to-pdf encoding issues):

Same YAML frontmatter as CV.

**Structure:**
- Name as H1 heading
- Contact info
- Horizontal rule
- **Til:** contact person and company
- **Dato:** today's date
- H2 with "Ansøgning: [job title]"
- Body: 300-400 words, max 1 A4 page — tighter and more concrete beats longer and padded. Twoday benchmark: ~350 words.

**Content rules:**
- Address to a specific person if known
- Never open with "Jeg søger stillingen som..." or "I am applying for..." - start with something concrete instead
- Do not open with "I saw your job posting"
- Show knowledge of the company and what they need
- Explain how Babak's experience solves their specific problems
- FITS placement rule: depends on role type.
  - **Software/engineering roles:** FITS is the main proof. Introduce it in paragraph 2, describe concretely (impact numbers, stack, tests). It demonstrates what Babak can build.
  - **GRC/consulting/security roles:** FITS goes in the LAST paragraph as a depth signal. The main proof is the consulting work (GlobalConnect, Nuuday, L7).
  - In either case: no more than 5 lines for FITS in the letter. The CV carries the detail.
  - Structure for engineering roles: (1) opening — Babak's work + bridge to role, (2) FITS as main proof + tests opinion sentence + AI components, (3) consulting background briefly, (4) honest handling of any gaps, (5) availability.
- Mention test focus: pytest, Jest, Playwright (unit, integration, end-to-end) — only when relevant to the role
- Mention vedholdenhed (persistence) as core strength
- Include motivation for working at the specific company. Do NOT ask the user about motivation before the interview step — use the interview answers instead. Derive additional motivation from the job listing itself: what does the company do, what problem do they solve, how does their domain connect to Babak's experience?
- Close with availability and willingness to interview
- Sign off: "Med venlig hilsen\nBabak Bandpey"

**Opening rule — start with Babak's concrete work:**

The opening paragraph should immediately establish what Babak does and bridge directly to why this role fits. Three short sentences hit harder than one long structured one.

**Two approved opening models:**

**Model A — Babak's work first (twoday/Todai model, preferred for engineering roles):**
> "Todai arbejder udelukkende med AI. Det er grunden til at denne stilling er interessant for mig. Ikke fordi AI er et buzzword. Fordi RAG-pipelines, LLM-agenter og chatbots i produktion er det, jeg faktisk arbejder med dagligt. Python og FastAPI er min kodebase. Det er præcis den type software engineering Todai vil styrke."

Starts with the company's context (1 sentence), then immediately Babak's concrete work, then bridge. No warmup.

**Model B — Company context first (Clinical Data Solutions model, works when the company situation is a strong hook):**
> "Clinical Data Solutions builds document generation and analytical tools for researchers working with large-scale datasets. Python/FastAPI, MongoDB, TypeScript, React, full CI/CD and TDD — that is my daily stack. The engineering problem is the same: make complex data accessible and remove the manual work that slows down the people who depend on the platform."

Their situation, then his stack, then common ground. Use when the company's problem/domain provides a compelling hook.

**How to choose:**
- If the role is primarily software/engineering: use Model A — start with Babak's concrete work
- If the company context provides a strong specific hook: use Model B
- **For GRC/consulting/security roles: always proof-first** — Babak's embedded years + what he did + context, then bridge to their need. Never open with the company's own program or initiative.
- Both must land within the first paragraph: what he does + why it matches + common ground

**Critical: don't restate the company's own situation back at them.**
"Økonomiforvaltningen er i gang med at opbygge et ISMS-program" — they know this. It wastes the first line and signals nothing.
Company context only works when it frames their domain to set up Babak's proof (e.g. "Todai arbejder udelukkende med AI" — a characterisation, not a restatement of their own project).

**Approved GRC opening (KK Økonomiforvaltningen, validated 2026-03-27):**
> "I tre år sad jeg embedded som GRC-konsulent hos GlobalConnect og Nuuday, begge NIS2-reguleret kritisk infrastruktur med sikkerhedsgodkendelse, og arbejdede direkte med ISO 27001-implementering, risikovurdering og ISMS-governance som daglig praksis. Det er præcis den profil I søger til ISMS-programmet i Økonomiforvaltningen."

One long proof sentence + one short match sentence. Not four fragments.

**Voice rules:**
- Short declarative sentences. They hit harder.
- One opinion sentence per application (see gap handling below for pattern)
- Do NOT open with Babak's identity, title, or years of experience
- Do NOT open with flattery ("spændende virksomhed...")
- Do NOT explain what the role requires back to the employer — lecturing
- Do NOT spend a paragraph building up to the match — land it immediately

Danish employers report the two most common weaknesses: insufficient description of professional match (55%) and unclear motivation (51%). A concrete, direct opening fixes both.

**Gap handling — twoday model:**
Admit the gap → confident bridge → one short punchy sentence → move on. Never dwell.

> "Azure er ikke det cloud-miljø jeg kender bedst. Jeg arbejder primært med Google Cloud og DigitalOcean. Men cloud er cloud. Principper og DevOps-tankegangen er de samme på tværs af udbydere. Azure specifikt er ikke noget der tager lang tid. Ikke med den baggrund jeg har."

The last short sentence ("Ikke med den baggrund jeg har.") carries the confidence without bragging.

**Sentence variety — avoid over-fragmentation:**
Natural Danish connects thoughts with conjunctions (og, men, fordi, så, eller). Too many consecutive short sentences ending with periods sounds telegraphic and AI-generated, just like sentences that are all the same length. The goal is natural flow, not staccato. Use a short punchy sentence for emphasis once or twice — not as the default rhythm throughout.

**"Jeg/du" balance (Danish norm):**
- Use "jeg" 5-8 times naturally — do NOT sanitize it out. An impersonal letter is worse than one with too many "jeg"s.
- Shift focus toward "du/I/jer" where possible — the letter should read as an offer to the employer, not a wish list for the applicant
- Never start consecutive sentences with "Jeg"
- Practical balance: personal without being private; employer-focused without being a template

**AI-feeling warning:**
45% of Danish employers think an application loses value when AI is used. The humanizer pass is not optional. Smooth, generic, well-structured text IS the red flag. Specific details, natural variation, and Babak's own phrasing from the interview are what makes it pass.

### Step 7: Run humanizer

**For Danish applications:** Run `humanizer-job-da` on the ansøgning (NOT the general humanizer-da — humanizer-job-da is a superset that includes job-application-specific patterns). Run humanizer-da on the CV.

**For English applications:** Run `humanizer` on both files.

The humanizer-job-da skill covers all of the below AND more. Key patterns to eliminate from both CV and ansøgning:

- **Em dashes (—) are BANNED**: never use em dashes anywhere. Use " - " (space-hyphen-space), periods, or commas instead
- **Tretalsmønstre**: don't force things into groups of three
- **Generiske vendinger**: "solid erfaring", "stærk i", "bred teknisk baggrund", "naturlig forlængelse"
- **Klichéer**: "ikke fremmed grund", "gang på gang vist"
- **Glatte formuleringer**: "trives med at analysere komplekse arbejdsgange og omsætte dem til stabile løsninger"
- **Reklamesprog**: "moderne frontend-udvikling", "produktionsklare platforme"
- **"Kombination af"**: always sounds AI-generated
- **"Motivationen er ægte"**: show, don't tell
- **"Jeg"/"I"/"me"/"my" balance**: CRITICAL - the applicant MUST be present in the text. A cover letter without "I" feels like a product description, not a person applying. Use "I"/"Jeg" naturally, 5-8 times per ansøgning. The rule is only: do not START every sentence with "I"/"Jeg". Vary the sentence structure, but keep the person in it. "My experience", "I built", "I have" are all fine when mixed with other sentence structures. An empty, impersonal text is WORSE than too many "I"s

After fixing, do a final anti-AI pass: ask "What makes this text obviously AI-generated?" and fix remaining issues.

### Step 8: Generate PDFs

Convert both markdown files to PDF:

Run as two parallel Bash tool calls — never chain with `&&`:

```bash
# Call 1:
cd [application-folder] && npx md-to-pdf cv.md

# Call 2 (parallel):
cd [application-folder] && npx md-to-pdf ansoegning.md
```

**NEVER use `2>&1`** — it delays output. Run without it.
**NEVER chain with `&&`** — run as two separate parallel Bash calls so they complete faster.

**If md-to-pdf hangs** (common with non-ASCII filenames):
1. Copy to ASCII filename: `cp ansøgning.md ansoegning.md`
2. Try md-to-pdf on the ASCII version
3. If still hangs, use `--as-html` then Chrome headless:
   ```bash
   npx md-to-pdf ansoegning.md --as-html
   google-chrome --headless --disable-gpu --no-sandbox --print-to-pdf=ansoegning.pdf --no-pdf-header-footer ansoegning.html
   ```

**CRITICAL: Always regenerate PDFs immediately after ANY edit to markdown files. Never ask, never skip, never batch edits without regenerating. Every set of changes = regenerate PDFs right away. The user should never have to ask for this.**

### Step 9: Update applied-jobs.md

Add the job to `./applied-jobs.md` with status "Ready".

### Step 10: Update session log

Append a brief entry to `./KNOWLEDGE-BASE/session-log.md`:
- Date (today)
- Company + job title
- Key tailoring decisions made (what was emphasized, what was omitted)
- Any open questions or follow-up needed

### Step 11: Check recruiter tracker

Read `./KNOWLEDGE-BASE/recruiter-outreach-tracker.md`.

If the company has a recruiter bureau that placed the job (e.g. job was posted via a known bureau from the tracker), note it. If the job was found through a bureau Babak hasn't contacted yet, flag it to the user.

### Step 12: Present summary

Show the user what was created and highlight any areas they might want to adjust. Include:
- Files created
- Any honesty flags (gaps between profile and requirements)
- Whether a bureau contact is relevant

---

## Knowledge base files to read before writing

Always read these before drafting CV or ansøgning:
- `./KNOWLEDGE-BASE/employment-history.md` — full work history with details
- `./KNOWLEDGE-BASE/cv-general.md` or `cv-general-en.md` — general CV as base
- `./KNOWLEDGE-BASE/projects-and-expertise.md` — project portfolio (if exists)
- `./KNOWLEDGE-BASE/tone-af-aabning-i-dansk-jobansoegninger.txt` — Danish employer research on what works and what gets rejected (opening patterns, match/motivation/proof structure, ranked opening templates)

Check session log for recent context:
- `./KNOWLEDGE-BASE/session-log.md`

---

## Language rules

- Match the language of the job listing: Danish listing = Danish CV + ansøgning, English listing = English CV + cover letter
- **All companies in this workspace are Danish.** Even when the job posting is in English, the audience is Danish. Danish cultural norms apply regardless of language:
  - Janteloven: understatement, let results speak — no bragging
  - Rolig selvtillid: calm confidence backed by proof, not self-promotion
  - humanizer-job-da rules apply even in English — translate the checklist, not just the skill name
- After drafting, run humanizer-job-da on the cover letter (Danish or English). Run humanizer-da on the Danish CV, humanizer on the English CV.
- Never mix languages within a single document

## What NOT to include

- Financial situation or urgency
- Desperation — it reads as a red flag
- Bragging or self-inflation — Danish work culture values understatement (janteloven)
- Technologies Babak hasn't actually used — be honest about gaps
- "60+ projects" — don't mention the number, instead say he enjoys development and has built personal projects alongside FITS

## What TO include (adapt per application)

- End-to-end ownership: backend, frontend, mobile, infra, security
- Vedholdenhed (persistence) — holds on to a project until it works properly
- Test-first mindset: pytest, Jest, Playwright, manual tests. Test results are what matters
- "Program the Programmer out of the Program" philosophy
- Concrete FITS achievements: setup time from 3 weeks to minutes, assessment evaluation from hours daily to minutes
- Security clearance for critical infrastructure (from GlobalConnect and Nuuday)
- Languages: Danish, English, Persian
- 25 years in the industry

## FITS facts (use accurately)

- **Database:** Neo4j (NOT PostgreSQL — Babak has PostgreSQL experience from other projects)
- **Backend:** Python/Django, Redis, Celery
- **Frontend:** Django templates, TypeScript, Tailwind (NOT React - React is used in other projects like FITSDK, nick-autoteknik, unity-foundation etc.)
- **AI:** AI agent that builds security policies, RAG-based conversations, automatic scoring, report generation
- **Data:** Import/export Word, PDF, Excel
- **Tests:** pytest, Jest, Playwright
- **Impact:** Setting up a GRC audit (defining scope, publishing questionnaires, sending to subject matter experts/SMEs) reduced from ~3 weeks to minutes. Processing SME responses and tracking compliance status reduced from hours daily to minutes (70-80% less manual work per audit cycle)
- **Ownership:** Built entirely by Babak, single-handedly

## Babak's actual tech stack (do not claim experience he doesn't have)

**Daily/strong:**
- Python, Django, FastAPI
- TypeScript, React, Next.js, Tailwind, Vite
- Neo4j, PostgreSQL, MySQL, MongoDB, Redis
- Docker, CI/CD, GitHub Actions, nginx, Linux
- Cloud: Hetzner (FITS production), DigitalOcean, Google Cloud
- AI/LLM: LangChain, OpenAI, Anthropic, RAG, MCP, Ollama
- Security: ISO 27001, CIS Controls, ISMS, NIS2, penetration testing, OSINT, RSA Archer
- Test: pytest, Jest, Vitest, Playwright

**Has experience with (not daily):**
- Kotlin, Jetpack Compose (Android apps)
- C#/.NET (Ballerup Tekniske Skole 2018-2019)
- Vue.js
- Oracle (Peter Justesen 2006-2011: ERP integration with Maconomy, Business Intelligence)

**Does NOT know:**
- Java / Spring Boot / Thymeleaf
- Angular
- AWS / Kubernetes / Terraform / OpenTofu (has Docker but not cloud infra-as-code)
- Event Sourcing / Event Modeling
- Apache Spark / big data tools

## Education

- Datamatiker — Niels Brock Copenhagen Business College, 2000
- IT-fag (C#/.NET) — Ballerup Tekniske Skole, 2018–2019
- Videregående uddannelse i cybersecurity — Erhvervsakademi, 2020

## Employment history

- cocode.dk — Senior Freelance (2018–now): FITS + 3 client projects + personal projects
- L7 Consulting — GRC Consultant (2020–2024): built FITS, 5-year engagement
- GlobalConnect — Security & Compliance, Phoenix Project (2022–2023): security cleared, NIS2
- Nuuday / Danish Telcos — GRC Consultant (2021–2022): RSA Archer, security cleared, NIS2
