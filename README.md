# job-jagger

> From invisible to interview-ready in one session.

AI-powered job application toolkit for [Claude Code](https://claude.ai/code).
Automates LinkedIn profile optimization and generates tailored CVs + cover letters for every vacancy.

**Built on real results:** 60+ applications in 2 weeks → 3 interviews in week 1.

---

## What's inside

| Skill | What it does |
|---|---|
| `/job-apply` | Fetch job URL → match analysis → tailored CV + cover letter → humanized → PDFs |
| `/linkedin-profile-editor` | Automate LinkedIn profile edits via browser. No manual UI. |
| `/humanizer-da` | Strip AI writing patterns from Danish prose |
| `/humanizer-job-da` | Cover-letter-specific humanizer for Danish |
| `/humanizer` | Same engine for English |
| `/outlook-email` | Send follow-up emails via Outlook Web from terminal |

---

## 🔗 LinkedIn Profile Editor — separate repo

> **[cocodedk/linkedin-profile-editor](https://github.com/cocodedk/linkedin-profile-editor)**
>
> The `/linkedin-profile-editor` skill lives in its own dedicated repo.
> It is a **skill + Playwright script library**: the skill orchestrates the flow,
> while 10 JS scripts handle React's synthetic event requirements that standard browser automation can't touch.
>
> Experience entries, skills reordering, open-to-work toggle, company typeaheads, date pickers. All from the terminal.
>
> ```bash
> npx add-skill cocodedk/linkedin-profile-editor
> ```

## Prerequisites

- [Claude Code](https://claude.ai/code)
- Node.js 18+
- Google Chrome

## Quick start

```bash
git clone https://github.com/cocodedk/job-jagger
cd job-jagger
```

Build your `KNOWLEDGE-BASE/` (see `KNOWLEDGE-BASE/README.md`), then:

```bash
claude
/job-apply https://jobindex.dk/...
```

## Full documentation

See [job-jagger.github.io](https://cocodedk.github.io/job-jagger) for the full guide,
proof of results, privacy options, and service offering.

## License

MIT © 2026 [Babak Bandpey](https://cocode.dk)
