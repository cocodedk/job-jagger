# job-jagger

AI-powered job application toolkit for Claude Code.
Automates LinkedIn profile optimization and generates tailored CVs + cover letters.

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- Node.js 18+
- Google Chrome (for PDF generation and browser automation)

## Setup

1. Clone this repo:
   ```bash
   git clone https://github.com/cocodedk/job-jagger
   cd job-jagger
   ```

2. Build your KNOWLEDGE-BASE (see `KNOWLEDGE-BASE/README.md`):
   - `cv-general.md` — your base CV in Danish
   - `cv-general-en.md` — your base CV in English
   - `employment-history.md` — detailed work history
   - `projects-and-expertise.md` — key projects

3. Start Claude Code:
   ```bash
   claude
   ```

## Workflows

**Apply for a job:**
```
/job-apply https://jobindex.dk/jobannonce/...
```
Fetches the listing, analyzes match, generates tailored CV + cover letter,
humanizes the text, exports print-ready PDFs.

**Optimize LinkedIn:**
```
/linkedin-profile-editor
```
Automates LinkedIn profile edits via browser automation — no manual UI.

**Humanize Danish text:**
```
/humanizer-da
```

**Humanize English text:**
```
/humanizer
```

## PDF Generation

```bash
cd <company-folder> && npx md-to-pdf cv.md
cd <company-folder> && npx md-to-pdf ansoegning.md
```

Fallback (Chrome headless):
```bash
node -e "
const fs = require('fs');
const {marked} = require('marked');
const md = fs.readFileSync('cv.md', 'utf8').replace(/^---[\s\S]*?---/, '');
const css = fs.readFileSync('../style.css', 'utf8');
const html = '<!DOCTYPE html><html><head><style>' + css + '</style></head><body>' + marked(md) + '</body></html>';
fs.writeFileSync('cv.html', html);
"
google-chrome-stable --headless --disable-gpu --no-sandbox --print-to-pdf=cv.pdf --no-pdf-header-footer cv.html
rm cv.html
```

## Key Rules

- **PDFs**: Regenerate after every markdown edit. Never ask, just do it.
- **Filenames**: ASCII only (ansoegning.md not ansøgning.md). Non-ASCII causes hangs.
- **Humanizer**: Always run before generating PDFs. Never skip.
- **Honesty**: Never claim experience you don't have.

## License

MIT — see LICENSE.

Built by [Babak Bandpey](https://cocode.dk) · [LinkedIn](https://linkedin.com/in/babakbandpey)
