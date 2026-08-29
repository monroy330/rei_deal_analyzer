# RESUME HERE: REI Deal Analyzer

## What this is
A single-page web app for real estate wholesalers: three calculators (Wholesale, Rental, BRRRR)
plus investor-facing deal sheets. Live at https://reidealanalyzer.netlify.app

## Source of truth (read this first)
`CLAUDE.md` in this folder. It has the deploy rule, full architecture, field conventions,
scoring spec, product decisions, and the GHL pre-fill URL notes. Everything below is a pointer.

## Deploy caution (hard rule)
- NEVER `git push` without Gabe's explicit yes. Push to `main` auto-triggers a Netlify
  build and Gabe treats Netlify deploys as scarce (see netlify-deploy-frugality memory).
- Free without permission: editing, local preview (just open `index.html` in a browser),
  and local `git commit`.
- GitHub: `monroy330/rei_deal_analyzer`, branch `main`.

## Where the code is
- `index.html`: the ENTIRE app (vanilla HTML/CSS/JS, no build step). This is what Netlify serves.
- `.claude/launch.json`: leftover from a deleted preview server; CLAUDE.md marks it dead/unused.

## Working style
Edit `index.html` directly, preview in a browser, ASK before pushing, then commit and push.
CLAUDE.md also asks for a proactive end-of-session memory check; honor it.
