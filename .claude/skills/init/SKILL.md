---
name: init
description: First-time setup for job-jagger. Use when KNOWLEDGE-BASE is missing or empty. Collects the user's background, compiles employment-history.md, cv-general.md, and cv-general-en.md, and sets up all required tracking files.
argument-hint: "[optional: paste your existing CV here]"
---

# Job-Jagger Initialisation

Set up the KNOWLEDGE-BASE from scratch so `/job-apply` is ready to use.

> **Paths:** All paths are relative to the project root (the directory containing `CLAUDE.md`).

---

## Step 0: Check current state

- Check what already exists in `./KNOWLEDGE-BASE/`
- If `employment-history.md`, `cv-general.md`, and `cv-general-en.md` all exist and are non-empty, tell the user:
  ```
  KNOWLEDGE-BASE already set up. Run /job-apply <url> to create an application.
  If you want to update your profile, edit the files in KNOWLEDGE-BASE/ directly.
  ```
  Then stop.

---

## Step 1: Collect raw input

Tell the user:

```
Welcome to job-jagger setup. This will take 5-10 minutes.

To get started, please share one or more of the following — whatever you have:

1. Your existing CV (paste the text, any format is fine)
2. Your LinkedIn profile URL (I will fetch it if accessible)
3. A brief description of your work history and skills

You can paste a messy, unformatted CV — I will clean it up.
If you have nothing prepared, just answer the questions I'll ask next.
```

If the user provides a CV or text: extract as much as possible from it before asking questions.
If the user provides a LinkedIn URL: use WebFetch to extract profile content.
If the user has nothing: proceed to Step 2 directly.

---

## Step 2: Interview — fill the gaps

Ask only what is missing after processing any input from Step 1. Group questions into one message — do not ask one at a time.

**Always ask (unless already clear from input):**

```
A few questions to complete your profile:

1. Full name
2. Location (city, country)
3. Phone number and email
4. LinkedIn profile URL
5. GitHub profile URL (if applicable)
6. Personal website (if applicable)

Work history (for each role, from most recent):
- Company name, your title, start and end dates
- What did you actually do day-to-day? (be specific — technologies, responsibilities, achievements)
- Any notable results or impact?

Education:
- Degree/qualification, institution, year completed

Languages:
- Which languages do you speak and at what level?

Skills:
- What are your strongest technical skills?
- What tools, frameworks, or platforms do you use daily?
- What have you used in the past but not currently?
- What are you learning or interested in moving toward?

Optional but useful:
- Do you have a personal description of yourself professionally — how you work, what drives you?
- Are there things you prefer NOT to do or roles you want to avoid?
```

Wait for answers before proceeding.

---

## Step 3: Compile `KNOWLEDGE-BASE/employment-history.md`

Create `./KNOWLEDGE-BASE/employment-history.md` with the full work history in this format:

```markdown
# Employment History

## [Job Title] — [Company] ([Start year]–[End year or "present"])

**Location:** [city]
**Type:** [employed / freelance / consultant]

### Responsibilities
- [specific responsibility]
- [specific responsibility]

### Technologies & tools
- [tech, framework, platform]

### Key achievements
- [concrete result or impact]

---
```

Include every role. Be detailed — this is the source of truth that all CVs are generated from.

---

## Step 4: Compile `KNOWLEDGE-BASE/cv-general.md` (Danish)

Create `./KNOWLEDGE-BASE/cv-general.md`:

```markdown
---
stylesheet: ../style.css
pdf_options:
  margin:
    top: 20mm
    bottom: 20mm
    left: 22mm
    right: 22mm
---

# [Full Name]

[Job title] | [X] års erfaring

[Phone] | [Email] | [LinkedIn] | [GitHub] | [Website]

---

## Profil

[5-7 lines: who they are, what they bring, written in Danish, first person, specific and concrete — not generic]

## Kernekompetencer

- [skill relevant to their background]
- [skill]
- [skill]
- [skill]
- [skill]

## Erhvervserfaring

### [Job Title] — [Company] ([dates])

[2-4 bullet points: what they did, what they built, what they achieved]

[repeat for each role, most recent first]

## Uddannelse

- [Degree] — [Institution], [year]

## Sprog

- [Language] ([level])
```

Write in Danish. Keep to 1-2 pages. Focus on results, not job descriptions.

---

## Step 5: Compile `KNOWLEDGE-BASE/cv-general-en.md` (English)

Create `./KNOWLEDGE-BASE/cv-general-en.md` — same structure as Step 4 but in English.

Adapt the profile section for an international audience while keeping the same concrete, results-focused tone.

---

## Step 6: Set up tracking files (if missing)

Create any of these that do not already exist:

**`./applied-jobs.md`:**
```markdown
# Applied Jobs

| Date | Company | Position | URL | Status |
|---|---|---|---|---|
```

**`./KNOWLEDGE-BASE/session-log.md`:**
```markdown
# Session Log

Log of each job application session. Read at session start to resume context.

---
```

**`./KNOWLEDGE-BASE/recruiter-outreach-tracker.md`:**
```markdown
# Recruiter Outreach Tracker

| Date | Name | Company | Channel | Status | Notes |
|---|---|---|---|---|---|
```

---

## Step 7: Update CLAUDE.md with user's name

Read `./CLAUDE.md`. If it still contains "Babak Bandpey" and the user is someone else, replace the name and profile description with the user's actual information.

---

## Step 8: Confirm ready

Tell the user:

```
Setup complete. Your KNOWLEDGE-BASE contains:

- employment-history.md — [N] roles documented
- cv-general.md — Danish CV
- cv-general-en.md — English CV

You can now run:
/job-apply <job listing URL>

Tip: Review the files in KNOWLEDGE-BASE/ and add any details you think are missing.
The more context you provide, the better the tailored applications will be.
```
