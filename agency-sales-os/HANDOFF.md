# Agency Sales OS — Handoff Document
**Session date:** 9 April 2026
**To pick up next session:** Paste this entire document to Claude and say "Continue building the Agency Sales OS"

---

## THE APP IN ONE LINE
A full sales OS for a Belfast video agency — AI activity logging, lead panels, pipeline, scripts, commission tracking, RingCentral calling, Fireflies transcripts, CSV import, weekly AI reports.

## CRITICAL CONTEXT
- Single-file HTML app (React 18 via CDN, no build step)
- All data in localStorage (key: sos_final_v1)
- API keys in localStorage (key: sos_keys_v1)
- Anthropic API called directly from browser (fine for internal tool)
- Model: claude-sonnet-4-20250514

## FILES
- `index.html` — the whole app, 1000+ lines
- `HANDOFF.md` — this file
- `PROJECT_STATE.md` — full technical spec

## 10 TABS
📋 BDO Daily | 💬 AI Logger | 👥 Leads | 💼 Pipeline | 📅 Cadence | 📥 Import CSV | 📝 Scripts | 💰 Commission | 📊 Report | 📞 Call Central

## KEY FEATURES TO KNOW
1. **Lead Side Panel** — slides from right, fully editable fields, activity timeline, linked deal, quick log, AI sync
2. **AI Logger** — natural language → logs activity + matches script + auto-opens lead panel
3. **Call Central** — RingCentral API dial, live timer, Fireflies transcript pull, post-call log
4. **Cadence dots** — clickable (manual) + auto-mark from logged activities
5. **Script leaderboard** — 🏆 Winner (≥15% booking, ≥5 uses) / 💀 Kill it (<5%, ≥10 uses)
6. **Commission** — 7% tracked per deal, per closer, mark paid, unpaid warning

## CONFIG (top of script block in index.html)
```js
const AGENCY_NAME    = 'Agency Sales OS';
const DEAL_AMOUNT    = 6900;
const COMM_RATE      = 0.07;
const TARGET_ACTIONS = 100;
const TARGET_MEETINGS = 6;
const LOCATION       = 'Belfast';
const CURRENCY       = '£';
```

## DESIGN TOKENS
Green: #1e9e5e | Purple (commission): #7c3aed | Orange (RingCentral): #ff6600
Font: Libre Baskerville (headings) + Arial (body)

## NEXT PRIORITIES (suggested)
1. Regenerate full zip with ALL features (lead panel + Call Central + cadence dots all merged)
2. Supabase integration for cross-device sync
3. Mobile layout for BDO in field
4. Email/SMS sending from lead card
5. Multi-BDO dashboards

## TO DEPLOY RIGHT NOW
Drag `index.html` to **app.netlify.com/drop** — live in 10 seconds, free, no account needed.
Add RingCentral + Fireflies keys in ⚙️ Settings tab once live.

---
*This project was built entirely in Claude.ai in one session.*
*All features communicate through a single central state object updated via React's useCallback pattern.*
