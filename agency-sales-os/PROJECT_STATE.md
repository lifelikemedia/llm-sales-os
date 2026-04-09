# Agency Sales OS — Project State
**Last updated:** 9 April 2026
**Status:** Feature-complete v1 · Ready for deployment

---

## What This Is
A full custom sales operating system built for a Belfast creative video agency.
- Product: Done-for-you video packages at £6,900
- Market: Belfast/NI SMEs, £1m+ revenue, 10–100 employees, Owner/MD/CEO
- BDO does 100 outbound actions/day targeting 6 meetings/week, 1 deal/week
- Commission: 7% per deal per closer

---

## Tech Stack
- **Deployment:** Single-file HTML (index.html) — no build step, works anywhere
- **Framework:** React 18 (CDN, no npm)
- **AI:** Anthropic claude-sonnet-4-20250514 via direct API call
- **Storage:** localStorage (key: sos_final_v1)
- **Integrations:** RingCentral API + Fireflies GraphQL API
- **Fonts:** Libre Baskerville (headings) + Arial (body)

---

## Design System
```
--ac:  #1e9e5e  (green accent)
--acl: #e8f7ef  (green light)
--acm: #a8dfc0  (green mid)
--acd: #157a48  (green dark)
--am:  #d97706  (amber)
--rd:  #dc2626  (red)
--bl:  #2563eb  (blue)
--pu:  #7c3aed  (purple — commission)
--rc:  #ff6600  (RingCentral orange)
--tx:  #1a1f17  (text)
--bg:  #ffffff  (background)
--bg2: #f7f8f5  (page background)
```

---

## CONFIG BLOCK (top of script in index.html)
```js
const AGENCY_NAME    = 'Agency Sales OS';
const DEAL_AMOUNT    = 6900;
const COMM_RATE      = 0.07;       // 7%
const TARGET_ACTIONS = 100;        // daily
const TARGET_MEETINGS = 6;         // weekly
const LOCATION       = 'Belfast';
const CURRENCY       = '£';
```

---

## 10 Tabs Built

| Tab | Key Features |
|-----|-------------|
| 📋 BDO Daily | Activity counter vs 100/day target, progress bar, today's feed with script used, lead queue, commission earned |
| 💬 AI Logger | Natural language → Claude logs activity + matches script + opens lead panel. Chips for quick send. |
| 👥 Leads | Full table, filter by status, add lead modal, touches count, click row to open lead panel |
| 💼 Pipeline | Draggable kanban (5 stages), commission per card, closer, terms, potential commission in header |
| 📅 Cadence | 14-day plan, clickable dots (manual + auto-mark from activities), "Next: Day X" indicator |
| 📥 Import CSV | Drag & drop CSV, AI column mapping, Levenshtein fuzzy match, auto-segment A/B/C, duplicate detection, sample data |
| 📝 Scripts | 11 preloaded scripts, channel filter, view/hide copy, active/inactive toggle, live perf stats, 🏆 Winner / 💀 Kill it badges |
| 💰 Commission | Revenue Won, Commission Earned, Unpaid Commission, by-closer table, deal tracker with Mark Paid, script leaderboard |
| 📊 Report | Streaming AI weekly report, revenue + commission included, 🚨/⚠️/✅ flags |
| 📞 Call Central | RingCentral API dial, live call timer, call state machine, Fireflies transcript pull, post-call log form, auto-cadence mark |

---

## Lead Side Panel (slides from right)
Triggered by: clicking any lead row, AI Logger logging against a lead, Cadence "Open card" button

**Contains:**
- Avatar with initials
- Editable: name, role, company, phone, email, LinkedIn, address, website
- Editable: industry, revenue band, employees, source
- Status selector (clickable pills — New/Working/Meeting booked/Customer/Disqualified)
- Segment selector (A/B/C clickable pills)
- Linked deal card (stage, amount, commission, terms, close date)
- Editable notes (auto-save on blur)
- Quick log form (type, outcome, script, cadence day, note)
- Activity timeline (reverse chron, shows script used, outcome pill, note, date)
- "AI Logger" button → goes to chat tab

**AI Sync:** When AI Logger logs against a lead, panel auto-opens for that lead and updates in real time

---

## 11 Preloaded Scripts

| ID | Name | Channel |
|----|------|---------|
| s1 | Cold Call Opener A — Time Check | Call |
| s2 | Cold Call Opener B — Pattern Interrupt | Call |
| s3 | Cold Call Opener C — Hyper-Local (inactive) | Call |
| s4 | Email Day 1 — Problem + Visit Offer | Email |
| s5 | Email Day 3 — Mini Case Study | Email |
| s6 | Email Day 6 — Break-up | Email |
| s7 | LinkedIn Connection Note | LinkedIn |
| s8 | LinkedIn First DM | LinkedIn |
| s9 | Voicemail Script | Voicemail |
| s10 | SMS After Voicemail | SMS |
| s11 | Door Knock Opener | Door Knock |

---

## RingCentral Integration
- **Auth:** JWT Bearer token (no OAuth redirect needed)
- **Endpoint:** POST /restapi/v1.0/account/{accountId}/extension/~/ring-out
- **Call states:** idle → ringing → connected → ended
- **Demo mode:** auto-simulates if no keys configured
- **Keys needed:** Client ID, Client Secret, Account ID (optional, defaults ~), JWT Token

## Fireflies Integration  
- **Endpoint:** POST https://api.fireflies.ai/graphql
- **Query:** transcripts(limit:1) → id, title, summary, analytics
- **Pulls:** summary bullets, action items, talk ratio, questions asked, objections, duration
- **Auto-saves:** transcript summary appended to lead notes
- **Demo mode:** realistic mock data generated if no key
- **Setup:** Fireflies → Integrations → RingCentral → Connect (Fireflies auto-joins calls)

## Settings Tab
- RingCentral: Client ID, Client Secret, Account ID, JWT Token
- Fireflies: API Key
- Stored in localStorage key: sos_keys_v1
- Instructions for getting each credential built into the UI

---

## Data Model (localStorage key: sos_final_v1)
```js
{
  leads: [{
    id, name, company, role, phone, email,
    linkedin, address, website, industry,
    revenue, employees, seg (A/B/C),
    source, status, createdAt, notes
  }],
  activities: [{
    id, leadId, leadName, type, outcome,
    scriptId, day (1-14), note, loggedAt
  }],
  deals: [{
    id, company, leadId, leadName, stage,
    amount, terms, closeDate, closer,
    commRate (0.07), paid (bool)
  }],
  scripts: [{
    id, name, channel, useCase, active, copy, notes
  }],
  manualDays: { "leadId_day": true },  // cadence manual marks
  chatHistory: [{ role, content }]      // last 20 messages
}
```

---

## ICP Segment Scoring
```
Belfast location     = +4
NI region            = +3
Ireland/UK           = +1
Owner/MD/CEO/Founder = +3
Director/Head of/VP  = +2
Manager/Marketing    = +1
10–100 employees     = +2
101–200 employees    = +1

Score ≥7 = A | ≥4 = B | else C
```

---

## Script Performance Logic
- Every Activity logs a scriptId
- Booking rate = (Booked meeting + Positive reply) / total uses × 100
- 🏆 Winner: rate ≥15% AND uses ≥5
- 💀 Kill it: rate <5% AND uses ≥10

---

## What's NOT Built Yet (next session ideas)
- [ ] Supabase sync — replace localStorage so BDO mobile + founder desktop share live data
- [ ] Mobile-optimised layout for BDO in the field
- [ ] Email sending integration (send scripts directly from lead card)
- [ ] LinkedIn DM integration
- [ ] Multi-user / multi-BDO with individual dashboards
- [ ] WhatsApp/SMS sending from lead card
- [ ] Lead enrichment (pull company data from Companies House / LinkedIn)
- [ ] Goal tracking dashboard (weekly/monthly revenue targets)
- [ ] Export to CSV / Google Sheets
- [ ] Notification system (remind BDO at Day 3, 6, 8 etc)
- [ ] Dark mode

---

## Deployment
**Current status:** Single-file index.html ready to deploy

**Fastest deployment (Netlify Drop):**
1. Go to app.netlify.com/drop
2. Drag index.html in
3. Live URL in 10 seconds — no account needed

**Vercel (Next.js version — for API key security):**
1. Unzip sales-os.zip
2. npm install
3. Add ANTHROPIC_API_KEY to .env.local
4. vercel (CLI) or drag to vercel.com/new

**Important for index.html version:**
- Anthropic API key is exposed in the browser
- Fine for internal team tool, not for public-facing product
- For security: use the Next.js zip version which keeps key server-side

---

## Files Delivered This Session
- `/mnt/user-data/outputs/index.html` — latest single-file version
- `/mnt/user-data/outputs/sales-os.zip` — Next.js version (older, missing some features)

**⚠️ Next session:** Regenerate the zip from scratch incorporating all features including lead panel, Call Central, Fireflies, clickable cadence, commission tab

