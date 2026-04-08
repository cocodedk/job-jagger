---
name: linkedin-profile-editor
description: Use when automating LinkedIn profile edits via Chrome DevTools MCP + Playwright MCP - adding/editing experience entries, skills, open-to-work settings, or deleting entries. LinkedIn is a React SPA with specific navigation and form-filling quirks that break standard Playwright patterns.
---

# LinkedIn Profile Editor

## Overview

LinkedIn is a React SPA. This skill uses **two MCP servers** with distinct responsibilities:

- **Chrome DevTools MCP** — primary driver for navigation, page state, clicking, scrolling, screenshots, and element inspection
- **Playwright MCP** — used exclusively for `browser_evaluate` to inject React synthetic events (form filling)

Plain DOM manipulation (`.value =`) is ignored by React — all form inputs require `nativeInputValueSetter` + synthetic events via the scripts in `scripts/`.

**All form-filling scripts are in `scripts/`. Read the file, substitute the UPPERCASE placeholders, then pass to `browser_evaluate` (Playwright MCP).**

**CRITICAL: Always use the scripts for form fields. Never write equivalent logic inline.**

---

## MCP Tool Reference

| Tool | MCP Server | Use for |
|---|---|---|
| `navigate_page` | **Chrome DevTools** | All URL navigation |
| `take_snapshot` | **Chrome DevTools** | Get element UIDs, page structure, accessibility tree |
| `click` | **Chrome DevTools** | Click buttons/links by element UID |
| `fill` or `type_text` | **Chrome DevTools** | Type into inputs by element UID |
| `press_key` | **Chrome DevTools** | Scroll page or dialog |
| `take_screenshot` | **Chrome DevTools** | Take screenshots |
| `evaluate_script` | **Chrome DevTools** | AI-powered content extraction via JavaScript |
| `evaluate_script` | **Playwright** | Run JS scripts for React form filling |

> **Note:** Chrome DevTools MCP uses UIDs (unique identifiers) instead of indices. Always use `take_snapshot` before interaction to get current UIDs — they change after DOM mutations.

---

## Core Rules

1. **Use Chrome DevTools `navigate_page` for ALL navigation** — it handles LinkedIn redirects correctly (tested: `/feed/` → login redirect followed without issues)
2. **NEVER set input values without dispatching React events** — React ignores plain `.value =` without `input`/`change` events
3. **Use `take_snapshot` before every `click`** — element UIDs change after DOM mutations (dialog open/close, typeahead appearing)
4. **Scripts use arrow function pattern**: `() => { const param = 'VALUE'; ... }` — substitute `'VALUE'` before running. Do NOT use IIFE `((p) => {})(val)` — `evaluate_script` requires a plain `() => {}` wrapper.
5. **Accessibility tree provides precise element targeting** — `take_snapshot` returns complete DOM with semantic structure, making form field location and interaction more reliable than index-based navigation

---

## Scripts (Playwright `browser_evaluate` only)

| Script | Purpose | Params |
|---|---|---|
| `inspect-dialog.js` | Show all fields, IDs, values, buttons in open dialog | none |
| `fill-position-form.js` | Fill title, employment type, checkbox, description | `TITLE`, `EMPLOYMENT_TYPE`, `currentlyWorking`, `DESCRIPTION` |
| `fill-company-typeahead.js` | Type company name + wait + select from suggestions (3 steps) | `COMPANY_NAME` |
| `fill-dates.js` | Set start and end date selects | `START_MONTH`, `START_YEAR`, `END_MONTH`, `END_YEAR` |
| `add-skill.js` | Add one skill to entry (run once per skill) | `SKILL_NAME` |
| `delete-entry.js` step 2+3 | Click Delete + confirm (step 1 replaced by `browser_navigate`) | `USERNAME`, `ENTRY_ID` |

> `navigate-to-edit.js`, `open-add-position.js` step 1, and `save.js` are **replaced by browser-use MCP tools** — see workflows below.

---

## Workflow: Add New Experience Entry

```
1. navigate_page           → https://www.linkedin.com/in/USERNAME/details/experience/
2. take_snapshot           → find the + button ("Add a position or career break")
3. click                   → click the + button
4. take_snapshot           → find "Add position" menu item
5. click                   → click "Add position"
6. evaluate_script         → inspect-dialog.js (verify dialog opened, note field IDs)
7. evaluate_script         → fill-position-form.js
8. evaluate_script         → fill-company-typeahead.js step 1
   take_snapshot           → check for suggestions (wait 1-2s if none)
   evaluate_script         → fill-company-typeahead.js step 2 (check suggestions)
   evaluate_script         → fill-company-typeahead.js step 3 (click match)
9. evaluate_script         → fill-dates.js
10. take_snapshot          → find "Save" button UID
    click                  → click Save
11. evaluate_script        → add-skill.js × N (one per skill)
```

## Workflow: Edit Existing Entry

```
1. navigate_page           → https://www.linkedin.com/in/USERNAME/details/experience/edit/forms/ENTRY_ID/
2. evaluate_script         → inspect-dialog.js (verify + check field IDs)
3. evaluate_script         → fill-position-form.js / fill-dates.js / etc. as needed
4. take_snapshot           → find "Save" button UID
   click                   → click Save
```

## Workflow: Delete Entry

```
1. navigate_page           → https://www.linkedin.com/in/USERNAME/details/experience/edit/forms/ENTRY_ID/
2. evaluate_script         → delete-entry.js step 2  (click Delete button)
3. evaluate_script         → delete-entry.js step 3  (confirm)
   → URL returns to /details/experience/ on success
```

---

## Chrome DevTools MCP Advantages

Chrome DevTools MCP provides superior element targeting and page inspection compared to traditional browser automation:

| Feature | Benefit |
|---|---|
| **Full accessibility tree** | Every element has a UID; semantic HTML structure is visible |
| **Precise form detection** | Form fields identified with unique IDs before interaction |
| **State inspection** | Snapshots capture current page state after every mutation |
| **Redirect handling** | Automatically follows HTTP redirects without manual intervention |
| **DOM snapshots** | Full HTML structure available for analysis before clicking |

**Pro tip:** Always call `take_snapshot` immediately after navigation and after major interactions (dialog opens, typeahead appears) to get fresh UIDs. Don't re-use UIDs from previous snapshots.

---

## Finding Entry IDs

Use `navigate_page` to the experience page, then `evaluate_script` with this function to extract all edit URLs:

```javascript
(() => {
  return Array.from(document.querySelectorAll('a[href*="/edit/forms/"]'))
    .map(l => l.href).join('\n');
})()
```

Or take a `take_screenshot` — pencil/edit buttons link to the edit form URL containing the ID.

---

## Employment Type Values

LinkedIn uses **numeric** values in the select — pass the number as a string:

| Value | Label |
|---|---|
| `2` | Contract |
| `12` | Full-time |
| `11` | Part-time |
| `3` | Self-employed |
| `20` | Freelance |
| `18` | Internship |
| `19` | Apprenticeship |
| `21` | Seasonal |

---

## Common Mistakes

| Mistake | Fix |
|---|---|
| Using Playwright `navigate_page` → stays on old page | Use **Chrome DevTools** `navigate_page` instead — it handles LinkedIn redirects |
| Input value doesn't stick | Scripts use `nativeInputValueSetter` + React events — make sure you're using the script, not inline `.value =` |
| `click` hits wrong element | Always run `take_snapshot` immediately before clicking — UIDs shift after DOM changes |
| Company typeahead shows nothing | Wait 1-2s after step 1 before checking suggestions |
| Save button not found | Run `take_snapshot` to get current UIDs; check that the button is visible in the snapshot |
| Delete URL 404 | LinkedIn has no `/delete/` URL — always go through edit form → Delete button |
| "Add skill" dropdown closes after each pick | This is expected — run `add-skill.js` once per skill |
| Typed skill name into Title field instead of skill input | The dialog has TWO inputs that look similar. Always use `add-skill.js` — it targets `input[placeholder*="Project Management"]` specifically |
| `add-skill.js` says "Add skill button not found" | Scroll down inside the dialog first — use `press_key` to scroll or `evaluate_script` with scroll logic. `add-skill.js` handles both cases |
| Skill shows "No results found" under "Currently in your Skills section" | Normal — look under "Additional skills" in the same dropdown |
| Modal file upload dialogs won't respond to clicks | LinkedIn's React modals have complex event handling. Target file inputs directly with `evaluate_script` rather than clicking visible buttons |
| Stuck on a complex interaction after multiple attempts | Use `evaluate_script` with detailed DOM inspection and manual form filling as a fallback |
