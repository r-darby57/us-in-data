# WORK_LOG.md

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
