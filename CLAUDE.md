# REI Deal Analyzer

A single-page web app for a real estate wholesaler (Gabe Monroy) who runs deals through GoHighLevel (GHL). Three calculators + investor-facing deal sheets.

## ⚠️ DEPLOY RULE — READ FIRST
- **NEVER run `git push` without Gabe's explicit "yes."** Pushing to `main` auto-triggers a Netlify build/deploy and consumes his Netlify usage.
- Free / no permission needed: editing files, testing in the local preview, and **local** `git commit`s.
- Normal flow: edit `index.html` directly → test locally → **ASK** → commit → push. Always pause for a yes before the push. `index.html` is the single working file — no preview sandbox.

## End-of-session memory check (Claude: do this proactively — don't wait to be asked)
When a session is wrapping up — Gabe says things like "that's all", "we're good", "done for now", "thanks", or we just finished a meaningful change — pause and run a quick memory check:
- Scan the session for **durable** facts or decisions not already in this file (architecture, conventions, product decisions, gotchas, workflow). Ignore transient/one-off details.
- If something's worth keeping: propose the exact line(s) to add and **ask for approval before editing**. Keep it lean — prefer 0–2 tight lines over paragraphs.
- If nothing's worth saving, say so in one sentence; don't force it.
- Never edit this file or `git push` without Gabe's OK.
Gabe usually can't track what's important mid-build, so it's on Claude to raise this at the end.

## Stack & files
- **`index.html`** — the entire app (production; what Netlify deploys). Vanilla HTML/CSS/JS, no build step, no dependencies except the Google Fonts CDN.
- `brrrr-preview.html` — DEPRECATED early sandbox; no longer used. Edit `index.html` directly. (Safe to delete; ignore if present.)
- `brrrr-server.py`, `.claude/launch.json` — local preview server config only. (If this folder is renamed/moved: `brrrr-server.py` is path-independent, but `.claude/launch.json` has absolute paths — just regenerate it.)

## Deploy / hosting
- GitHub: `monroy330/rei_deal_analyzer`, branch **`main`** (auth token is embedded in the remote URL).
- Push to `main` → Netlify auto-deploys → https://reidealanalyzer.netlify.app
- To deploy: make sure changes are in `index.html`, commit, then (WITH PERMISSION) `git push origin main`. End commit messages with the Co-Authored-By line.

## Architecture (all inside index.html)
- Modes: `setMode('wholesale'|'rental'|'brrrr')` toggles `#section-*`.
- Calc engines: `calcWholesale()`, `calcRental()`, `calcBRRRR()` — run on every input via `oninput`.
- Data globals for the deal sheets: `_wData`, `_rData`, `_bData`.
- Deal sheets: `openSheet(mode)` → `buildWholesaleSheet()` / `buildRentalSheet()` / `buildBRRRRSheet()`; plus `buildDualExit()`, `sheetHead()`, `sheetFooter()`.
- Helpers: `val(id)` (number; blank OR "0" → 0), `isBlank(id)`, `fmt()` ($), `fmtPct()` (%), `txt(id)`.
- Toggle state + setters: down `setDownMode`; taxes/insurance Mo↔Yr `setTaxesMode`/`setInsuranceMode` (rental) and `setBTaxesMode`/`setBInsuranceMode` (BRRRR); refi $↔% `setRefiMode`; holding `setHoldMode`.
- Copy between tabs: `copyRentalToBRRRR()`, `copyBRRRRToRental()` (common fields only).
- Share/load: `shareURL(mode)` builds a URL of field IDs; `loadFromURL()` applies them on page load.

## Field ID conventions
`w-*` = Wholesale, `r-*` = Rental, `b-*` = BRRRR, `prop-*` = shared property info, `b-company/b-tagline/b-contact` = branding.

## Input rules (important)
- **No hidden defaults.** `val()` treats blank AND "0" as 0.
- **Optional fields** (vacancy, closing, refi costs, taxes, insurance, mgmt, maintenance, HOA, other): blank = 0.
- **Structural financing fields** (down payment, interest rate, loan term, refi LTV): GATED — if missing, show "—" + a hint; never compute with a phantom 0.

## Scoring spec (transparent; also commented in code)
Tier factor: green = 1.0 · orange = 0.5 · red = 0.0. Score = Σ(weight × tier), 0–100.
- **Rental** = CoC 40 (≥10 / 6–10 / <6) + Cash Flow 25 (≥250 / 100–249 / <100) + DSCR 20 (≥1.25 / 1.10–1.24 / <1.10) + Cap Rate 15 (≥8 / 6–8 / <6)
- **BRRRR** = Cash Left %ARV 40 (≤5 / 5–15 / >15) + Capital Recovery 30 (≥90 / 75–90 / <75) + CoC 20 (≥12 / 8–12 / <8) + Cash Flow 10 (same as rental)
- Badge: 90+ Excellent · 75+ Strong · 60+ Solid · 40+ Fair · <40 Weak. Score color: ≥75 green · 40–74 yellow · <40 red.
- **Guardrail:** negative cash flow is NEVER green (badge → "Loses Money Monthly"); thin-but-positive CF caps the color at yellow.
- Cash-flow words (both tabs): $250+ Strong · $100–249 Moderate · $0–99 Thin · <0 Negative.

## Key product decisions (don't regress these)
- **Wholesale fee is private.** On the buyer deal sheet, Purchase Price = MAO + fee (fee baked in, never itemized) so the fee stays OUT of the buyer's profit. MAO in the calculator = max offer to the *seller*.
- **Seller Asking Price** (optional, `w-asking`) drives the negotiation position — CALCULATOR-ONLY, never on the buyer PDF: ≤ MAO = Strong Negotiation Position · ≤5% over = At Target Range · >5% over = Price Reduction Needed.
- **Rental deal sheet** = financing-agnostic "Property Performance" (Rent, NOI, Cap Rate, OpEx) + a clearly-labeled "Example Financing Scenario" (down, cash needed, cash flow, CoC).
- Equity Captured = ARV − Total Cash Invested.

## GHL pre-fill URL
The GHL workflow builds a link where query params = field IDs and values = merge tags; the app applies it via `loadFromURL()` (numeric fields strip `$`/commas; comps use ` | ` ↔ newline because GHL strips newlines). Includes `w-asking={{opportunity.asking_price}}`. The full URL lives in the GHL workflow.
