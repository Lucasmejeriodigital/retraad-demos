# demo-build skill — design spec
Date: 2026-09-07 · Author: Lucas Mejer Hansen + Claude

## Problem

Building a client demo today requires manually scraping design tokens, writing HTML from scratch, and maintaining the index page by hand. Each demo takes hours and relies on context that lives in CLAUDE.md rather than being derived from the client's actual site.

## Goal

A `/demo-build` skill that reads a brief, scrapes the client's live site for design context, proposes a screen plan for approval, then builds each screen one at a time with screenshot feedback — producing linked, deployable HTML demos.

## Activation

```
/demo-build <brief-path> <client-url>
```

Examples:
```
/demo-build ~/Downloads/brief-demo3.docx https://www.ret-raad.dk
/demo-build "Vi vil vise en onboarding-flow for nye brugere" https://example.com
```

Both inputs are required. `brief-path` may be a file path (.docx, .md, .txt) or a quoted string of pasted text.

---

## Step 1 — Parse brief

Extract from the brief:
- **Project name** — used in filenames and `<title>` tags
- **Screen list** — which screens to build, in order, with one-line purpose each
- **Copy rules** — language constraints (e.g. "no legal terms in headings", "first person, active voice")
- **Technical constraints** — e.g. "must look buildable in WordPress/Gutenberg"
- **Open questions** — items the brief explicitly flags as unresolved; the skill proposes a concrete default for each

## Step 2 — Scrape client site

Open the client URL with Playwright. Extract and save to `site-context.json`:

| Asset | How |
|---|---|
| Logo | Find `<img>` or SVG in `<nav>`, download to `assets/logo-[client].svg` or `.png` |
| Color palette | Read CSS custom properties (`--c-*`, `--color-*`) + computed bg/text/border from nav, primary buttons, headings |
| Typography | `font-family`, weights, sizes from `body`, `h1`, `h2`, nav links |
| Nav structure | Top-level link labels and hrefs |
| Key copy | Hero headline, primary CTA text, tagline if present |

If `site-context.json` exists and is less than 7 days old, ask: "Site-kontekst fra [date] findes allerede — genscrab?" If user says no, skip to Step 3.

## Step 3 — Generate and present plan

Produce a structured text plan — no code yet:

```
Demo: [project name]
Kunde: [client URL]

Skærm 1 — [title]
  Fil: [filename].html
  Formål: [one sentence]
  Sektioner (prioriteret):
    1. [section name] — [what it contains]
    2. ...
  Tone: [key copy rules for this screen]

Skærm 2 — ...

Åbne spørgsmål → defaults:
  • [question] → [proposed default]
  • ...
```

Present the plan in chat. Wait for explicit approval before writing any code. If the user requests changes, revise and re-present.

## Step 4 — Build screens one at a time

For each screen in approved order:

### 4a — Build HTML

Write the HTML file. Rules:
- Use design tokens from `site-context.json` — not hardcoded values
- Use the logo asset downloaded in Step 2
- Apply `frontend-design` skill principles: opinionated layout, real copy from the brief, no lorem ipsum
- Single self-contained HTML file (no build step, no external dependencies beyond Google Fonts)
- Include `<!DOCTYPE html>`, `<html lang="da">`, `<head>`, `<body>`, `<main>`, `<footer>`
- Viewport meta tag always present

### 4b — Screenshot

Open the file in Playwright:
- Desktop: 1280×900 — top, mid, bottom (three screenshots)
- Mobile: 390×844 — top

Show screenshots in chat.

### 4c — Feedback loop

Ask: "Hvad skal ændres, eller er den klar til næste?"

If feedback given: apply changes, take new screenshot, repeat.
If approved: mark screen as done, move to next.

## Step 5 — Link screens

After all screens are approved:
- Add subtle prev/next navigation to each file: `← [prev screen title]` / `[next screen title] →` at the bottom, styled with the client's secondary link color
- Verify the click path works in Playwright (navigate through all screens in order)

## Step 6 — Update index.html

`index.html` is the central demo selector. The skill always adds to it, never removes.

Structure per demo set:
```html
<!-- Demo group card -->
<a href="[first-screen].html" class="demo-card">
  <div class="demo-num">[N]</div>
  <div class="demo-info">
    <div class="demo-title">[Demo name]</div>
    <div class="demo-desc">[One-line description]</div>
    <div class="demo-screens">
      <a href="[screen1].html">Skærm 1 — [title]</a>
      <a href="[screen2].html">Skærm 2 — [title]</a>
    </div>
  </div>
  <div class="demo-status status-done">Klar</div>
</a>
```

If `index.html` does not exist, create it with the Ret & Råd design system styling from CLAUDE.md.

## Step 7 — Commit and push

```
git add .
git commit -m "demo: [project name] — [N] skærme"
git push
```

Report the GitHub Pages URL.

---

## site-context.json schema

```json
{
  "url": "https://www.ret-raad.dk",
  "scraped": "2026-09-07T11:00:00Z",
  "logo": {
    "path": "assets/logo-ret-raad.svg",
    "original_url": "https://www.ret-raad.dk/..."
  },
  "colors": {
    "primary": "#214b0d",
    "secondary": "#1b642e",
    "accent": "#2a9b47",
    "background": "#f9f9f9",
    "text": "#0c0c0c",
    "raw_properties": { "--c-forest": "#214b0d" }
  },
  "typography": {
    "body_family": "'Barlow', sans-serif",
    "display_weight": 700,
    "body_weight": 400,
    "google_fonts_url": "https://fonts.googleapis.com/..."
  },
  "nav_links": [
    { "label": "Specialer", "href": "..." }
  ],
  "key_copy": {
    "hero_headline": "...",
    "primary_cta": "Find en advokat",
    "tagline": "..."
  }
}
```

---

## What this skill does NOT do

- Does not replace `demo-review` — run that after to catch issues
- Does not build production code — demos only, no CMS integration
- Does not scrape competitor sites or sites the user doesn't own
- Does not generate more than one demo set per invocation

---

## Success criteria

After running `/demo-build`:
1. All screens render correctly at desktop and mobile
2. Click path through all screens works
3. index.html shows the new demo alongside any existing ones
4. Changes are committed and pushed to GitHub Pages
5. No hardcoded client colors — everything comes from `site-context.json`
