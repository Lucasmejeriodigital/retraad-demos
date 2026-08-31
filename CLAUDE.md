# Ret & Råd — Demo Project

Agency: iO · Contact: Jens Poder
Deadline: Wednesday 2 September 2026
Goal: Three clickable HTML concept demos for ret-raad.dk redesign pitch

## What these are
Concept sketches, not production UI. Show principles to the client.
Each demo is a single self-contained HTML file — no build step, no dependencies.

## Priority order
1. **Demo 1** — `demo1-gaeldssanering.html` (MUST HAVE)
   Personalized specialty page for gældssanering. Frederiksberg-lokaliseret advokat,
   CTA to book call, social proof, action-oriented layout.

2. **Demo 2** — `demo2-digital-flow.html`
   Digital conversion path — chatbot/video as alternatives to physical meetings.

3. **Demo 3** — `demo3-user-journey.html` (only if time)
   User journeys in emotional language. Least defined — clarify with Jens first.

## Design system
Design tokens scraped from ret-raad.dk live site.
Reference file: `assets/design-system.html`

### Colors
- `--c-forest:     #214b0d` — primary dark green (logo, nav, headings, primary button)
- `--c-pine:       #1b642e` — medium green (footer bg, hover states)
- `--c-leaf:       #2a9b47` — accent green (category tags, CTAs, links)
- `--c-sage:       #d7e6d8` — light green tint (borders, bg tints)
- `--c-terracotta: #cc8080` — salmon (hero CTA button — the warm human contrast)
- `--c-ink:        #0c0c0c` — body text
- `--c-paper:      #f9f9f9` — page background
- `--c-white:      #ffffff`
- `--c-gray:       #d3d3d3`

### Typography
- **Font: Barlow only** — load from Google Fonts
  `https://fonts.googleapis.com/css2?family=Barlow:ital,wght@0,100;0,400;0,500;0,600;0,700;1,400&display=swap`
- Display: 700, ~3.875rem, lh 1
- H1: 600, clamp(1.875rem → 3.875rem), lh 1
- H2: 600, clamp(1.25rem → 2.1875rem), lh 1.1
- H3: 600, clamp(1rem → 1.5rem), lh 1.2
- Body: 400, 1rem, lh 1.6
- Labels: 500, 0.75rem, uppercase, 0.1em letter-spacing, color --c-leaf

### Layout
- Max-width: 1130px (content: 745px)
- Grid gutter: 20px · Grid col: 70px
- Spacing scale: 10, 20, 30, 40, 50, 60, 70, 80, 90, 100px
- Border-radius: **1px** (almost square — intentionally formal)
- Card shadow: `4px 3px 35px 5px rgba(0,0,0,.1)`
- Transition: `150ms ease-in-out`

### Key component patterns
- **Primary button**: `--c-forest` bg, white text, 2px border, 14px 24px padding
- **Secondary button**: transparent bg, `--c-forest` border + text
- **CTA button**: `--c-terracotta` bg, white text (used in hero)
- **Category tags**: `--c-leaf` border + text, 11px uppercase, 0.06em spacing
- **Footer**: always `--c-pine` (#1b642e) bg with white text
- **Nav**: white bg, `--c-forest` links, outlined "Find kontor" button
- **Cards**: border `--c-sage`, shadow, hover lifts 2px

## Publishing
Demos are served via GitHub Pages.
After building/updating a demo:
```
git add .
git commit -m "demo: <short description>"
git push
```
URL pattern: `https://<username>.github.io/retraad-demos/<filename>.html`

## What NOT to do
- No lorem ipsum — all copy must be real Danish
- No rounded corners beyond 1px
- Don't use a second font family — Barlow only
- Don't invent colors outside the 9-token palette
- Don't make it look like a generic SaaS landing page
