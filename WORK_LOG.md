# WORK_LOG.md

## 2026-05-29 — Custom domain, CSV cleanup, mobile polish, ship to Katie

Followup day after the overnight pass. Goal: make sure the site is presentable
enough to send to Katie, then send it.

### What changed

**CSV cleanup (analyze layer)**
- Strip `This message responded to an earlier message.` suffix that
  `imessage-exporter` appends to every quote-reply. Without this fix,
  "message" was the #1 top word in every year-tile (1,016 hits in Ryan's CSV).
- Collapse edited messages: body is `<pre-edit>\nEdited Xs later: <final>` —
  keep only the final text so word counts aren't doubled (117 hits).
- Strip leading repeated `Mon DD, YYYY H:MM:SS AM` timestamps on edited bodies.
- Strip attachment file paths (`/Users/.../Messages/Attachments/...`) — 970
  rows in Ryan's CSV were pure attachment paths. Mixed text+path rows keep
  the text; path-only rows fall through the empty-message filter.
- Treat `Find My: Started/Stopped Sharing Location` as system noise.
- Smarter "first I love you" — skip messages with URLs or Spotify/Apple Music
  metadata. The previous version's first hit was a Spotify share where the
  song title was "Burn Slowly / I Love You".

**Mobile**
- Story strip was `display:flex; overflow-x:auto` on mobile → forced horizontal
  scroll. Now `repeat(2, 1fr)` at ≤900px and single column at ≤480px so the
  whole page is vertically centered with no horizontal scroll.
- Added `canvas { max-width:100% }` and `.wcwrap canvas { display:block;
  max-width:100%; height:auto }` so word-cloud and Chart.js canvases shrink
  to their container instead of pushing the page wider.
- Verified `scrollWidth == clientWidth == window.innerWidth` at 500px (Chrome
  headless minimum).

**Site-blank bug**
- `.reveal` was `opacity:0` by default and only got an `.in` class once the
  IntersectionObserver was attached, which only happened inside `recompute()`
  after data loaded. Result: with no data the entire page below the hero was
  invisible. Split into `.reveal` (always animated) and `.reveal.pending`
  (only invisible if JS explicitly opted in). Default is visible.

**Password rotation**
- Rotated SHA-256 gate hash from a value Ryan forgot to `katielove2024`.
  Stored in macOS Keychain under service `relationship-dashboard` so I can
  recover it next session.

**Custom domain — ryanandkatie.love**
- `ryanandkatie.com` was registered in 2008 (Squarespace, Mailgun email).
  Picked `.love` instead — fits the tone anyway.
- `CNAME` file updated from `ryan-apps.pages.dev`-style default to
  `ryanandkatie.love`.
- DNS via Cloudflare API (zone `60b5efb2…b4654`): 4 A records to GitHub
  Pages (185.199.108–111.153) + `www` CNAME to `r-darby57.github.io`,
  all unproxied (Pages won't serve through the orange cloud).
- HTTPS cert was approved by Pages automatically; enforced via
  `gh api -X PUT repos/r-darby57/us-in-data/pages -F https_enforced=true`.
- Site live at https://ryanandkatie.love

**Typo domain — ryanandkaite.com**
- Bought first by accident. Cloudflare doesn't refund registrations once the
  registry processes them. Auto-renew turned off; domain will expire July 2027.
- Planned to set up 301 → `ryanandkatie.love` but the token didn't have
  redirect-rule perms and Ryan decided it wasn't worth the hassle.

**Privacy**
- Added `<meta name="robots" content="noindex, nofollow">` + googlebot
  variant. Keeps the URL out of search results.

### Sent to Katie

Final message + AirDropped `katie-messages.csv`. Site loaded, password
`katielove2024`, Browse for file → CSV → dashboard.

### Files

- `index.html` — heavy edits in the CSV-cleaning, mobile, and gate areas.
- `CNAME` — now `ryanandkatie.love`.
- `WORK_LOG.md` — this file.

### Decisions / context

- `cleanMessageText()` runs at the row level in `loadRows()`, before
  `isSystemMessage()`. Path-only messages become empty strings and fall
  through the existing `r.message && …` filter.
- `isGenuineILoveYou()` excludes URLs and Apple Music / Spotify markers.
  Heuristic, not perfect, but fine for our data.
- Cloudflare token to set up DNS was zone-scoped to all zones, used once,
  Ryan was prompted to revoke after.
- Mobile rendering was verified at 500px (Chrome's macOS headless minimum).
  Real iPhones at 375px will be narrower but use the same responsive rules.

### Open items

- Wait for Katie's feedback. Any tweaks come after that.
- Could add a 301 redirect from `ryanandkaite.com` later if Ryan changes his
  mind; the typo domain stays parked until 2027.

---

## 2026-05-27 — Overnight polish pass

Ryan loaded ~16k real messages and asked me to improve the dashboard while he slept. The goal: ship something he'd be excited to share with Katie.

### What changed

**Removed**
- Sentiment-over-time chart and its lexicon (POS_WORDS / NEG_WORDS). Per Ryan: "it's not obvious what it does." The section is now "Words & Emojis."
- "Heat by hour" Chart.js bar — replaced by a richer 7×24 heatmap.

**Added**
- **Story of Us strip** — four cards under the hero: first message, first "I love you", biggest day, longest streak. Rose→gold gradient borders, responsive 4-col → mobile horizontal scroll.
- **Activity heatmap** — 7-day × 24-hour grid showing when conversation happens. Sqrt intensity scaling for readable contrast; "Peak: Sundays around 9pm" hint underneath.
- **Conversation patterns card** — initiations, double-texts, typical vs. slowest-10% reply time. Replaces simple "median · avg" with more honest p50/p90 framing.
- **Year-by-year tiles** — per-year message count, top word, top emoji, most active day. Single-year case is centered at 280px so it doesn't stretch awkwardly.
- **Memory Lane → chat bubbles** — proper rose/gold message bubbles with asymmetric corners, sender labels only on first of a run, day header, timestamps under each bubble, spacing on sender change.
- **Hero count-up + scroll reveal** — total messages animates from 0 with ease-out cubic; sections fade in via IntersectionObserver. Honors `prefers-reduced-motion`.

**Mobile pass (375px)**
- `html { overflow-x: hidden; }` belt-and-suspenders.
- Hero h1: `text-2xl sm:text-5xl md:text-6xl` with `overflow-wrap:break-word; hyphens:auto`.
- Header chip abbreviates "100% Local" → "Local" below sm.
- Dropzone padding reduced on small screens.
- Heatmap row labels tighten to 22px below 480px.

### Files

- `index.html` — single-file dashboard, +544 / −152 lines.
- `/Users/ryan/Desktop/imessage-txt-to-csv.py` — converter from `imessage-exporter` txt → CSV (Sep 30 2023 → May 27 2026, 16,300 messages).
- `/Users/ryan/Desktop/katie-messages.csv` — Ryan's actual export. Stays local.

### Decisions / context

- Kept Chart.js for monthly line + donut (worth the CDN). Heatmap is pure CSS grid — no Canvas, no extra dep.
- Gate is unchanged (SHA-256 + sessionStorage). Don't commit the password.
- Chrome headless on macOS has a minimum 500px viewport, so verifying true 375px requires a real device. Layout is responsive (flex-wrap buttons, breakpoints, abbreviated chip) and renders cleanly at 500px, so it should be even tighter at 375.

### Open items

- Real-device check at 375px on iPhone — should "just work" but worth a glance.
- No keyboard nav audit on the new heatmap (it's decorative).
- Verify the live URL still loads after push: https://r-darby57.github.io/us-in-data/
