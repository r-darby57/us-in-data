---
name: Words & Sentiment section (Section 02) review findings
description: Known issues and improvement opportunities identified in the words/sentiment section of the dashboard
type: project
---

## Known issues (at time of review, 2026-05-27)
- Word cloud canvases render as empty boxes with demo data (bug being fixed by code-reviewer agent separately)
- "Not enough words yet" fallback is canvas fillText — no styled empty state
- Toggle controls (Exclude common words / Exclude custom) have no touch target padding — native checkbox + tiny text, sub-44px
- Toggle controls overflow on narrow viewports (flex row with no wrap)
- Three-card grid jumps directly from 1-col (mobile) to 3-col (lg) with no 2-col tablet step
- Word/bigram pills use dynamic font-size scaling (11–14px) but the count badge inside is just muted text — no visual separation
- Sentiment chart y-axis fixed suggestedMin/Max (-0.5 to 1.5) even when data is positive-only; zero line not highlighted
- Emoji section in third card is asymmetric — combined word cloud above, then 2-col emoji grid below with no visual divider
- "word cloud" badge label in card header is low-value repetition (all three cards say it)
- Bigram pills look identical to word pills — no visual distinction despite different data types
