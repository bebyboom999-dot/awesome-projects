# Implementation Plan — Making the AI Agent Team Actually Run

This plan assumes the 8 agents in [`.claude/agents/`](../.claude/agents) are already defined (see [`README.md`](README.md)) and answers a different question: **how do you turn 8 markdown agent definitions into a working system** that researches trends, plans content, scripts videos, publishes, talks to clients, and reports results — on a real cadence, with real tools.

Platform names, API programs, and review requirements below reflect how Meta/TikTok/Figma/Canva integrations work as of early 2026. Social platform API policies change often — re-verify current requirements (permissions list, review turnaround, rate limits) at the point you actually apply, since this is the part most likely to drift.

---

## 1. What platform to build and manage the agents on

Don't look for one platform that does everything — this system needs **two layers**, because "an AI agent" and "an automation that runs on a schedule and calls APIs" are different jobs.

### Layer 1 — Reasoning/creative brain: Claude
This is where the 8 agents actually "think" — trend analysis, concept development, script writing, campaign strategy, client replies. Two ways to run this layer, and you'll likely use both at different stages:

- **Claude Code (this repo)** — the agent definitions already live here as version-controlled files. Best for work you want to *supervise directly*: reviewing the weekly trend report, approving Creative Director concepts, editing an agent's instructions as your brand evolves. Everything stays in Git, so you have a history of how the agents' instructions changed over time.
- **Claude API (Anthropic API)** — the same agent system prompts (the body of each `.claude/agents/*.md` file), called programmatically. This is what lets an automation platform trigger an agent *without you typing a prompt* — e.g., "every Monday at 8am, call the Trend Hunter agent and post its report to Slack."

Practically: keep this Git repo as the **single source of truth** for each agent's instructions. When you wire up automation (Layer 2), it calls the Claude API using the exact same system prompt text stored here, so you only ever update an agent's behavior in one place.

### Layer 2 — Orchestration/execution: an automation platform
Claude can write a script, a caption, or a trend report — it cannot, by itself, run on a timer, watch for a new Instagram DM, or push a finished video to TikTok's API. You need an automation platform that:
- triggers agents on a schedule or event (new DM, new file in a folder, form submission),
- calls the Claude API with the right agent's system prompt,
- calls the actual platform APIs (Instagram, TikTok, Google Calendar, etc.) to take real action,
- and passes structured data between steps (trend report → concept → calendar slot → script → published post).

**Recommendation: [n8n](https://n8n.io).** Reasons specific to this system:
- Native or near-native nodes for Google Calendar, Airtable, Slack, HTTP-based APIs (covers Instagram/Threads/TikTok/Canva/Figma via their REST APIs even without a dedicated node).
- Self-hostable, so there's no per-task pricing ceiling as the system scales to daily posts + DMs + nightly analytics pulls.
- Handles the branching/approval logic this system needs (e.g., "wait for human approval before publishing").

**Alternative: [Make.com](https://make.com)** if you'd rather trade some flexibility for a more visual, slightly gentler learning curve — same architecture, higher cost at scale, less control if you outgrow the visual builder.

**Not recommended as the backbone:** Zapier — fine for simple one-step automations (e.g., "new booking → Slack message") but its branching/looping logic gets expensive and awkward for a multi-agent pipeline like this one.

---

## 2. Connecting the required tools

| Tool | What it's used for here | How you actually connect it |
|---|---|---|
| **Instagram** | Publishing, trend/competitor monitoring, DMs | Convert to an Instagram **Professional (Business or Creator) account**, link it to a **Facebook Page**, then connect via the **Instagram Graph API** (a Meta Developer App) |
| **Threads** | Publishing, monitoring | **Threads API** (Meta) — same Meta Developer App as Instagram, tied to the same professional account, separate permission scopes |
| **TikTok** | Publishing, trend/competitor monitoring | **TikTok for Developers** account → **Content Posting API** (publishing) + **Display API** (reading/insights) |
| **CapCut** | Video editing/finishing | No public automation API — this stays a **manual, human step**. The Reels Video Creator agent's output (script + shot list + CapCut edit spec) is handed to whoever is editing; they execute it by hand in CapCut desktop/mobile/web |
| **Figma** | Brand design system | **Figma REST API** (personal access token or OAuth) — already available in this workspace via the Figma MCP connector |
| **Canva** | Templates, brand kit, on-brand asset generation | **Canva Connect API** (Autofill + Asset + Export endpoints) — already available in this workspace via the Canva MCP connector |
| **Website** | Portfolio, booking entry point, SEO/discovery | Depends on your website platform (WordPress, Squarespace, Wix, custom). At minimum: embed the booking widget and a live Instagram feed; connect **Google Analytics 4** for traffic |
| **Booking calendar** | Appointment scheduling | **Google Calendar API** (already available) for the underlying calendar, plus (recommended) a **salon-specific booking platform** — Fresha, Booksy, or Treatwell — layered on top for deposits/no-show protection/client-facing booking page, synced to Google Calendar |
| **CRM** | Client history, retention, lead tracking | Start with **Airtable** (already available) as a lightweight CRM; migrate to a dedicated CRM only if/when Airtable's manual structure becomes a bottleneck |
| **Analytics** | Content + campaign performance | Native insights (Meta Business Suite for IG/Threads, TikTok Analytics) + **Google Analytics 4** (website) aggregated through **Supermetrics** (already available) into one dashboard |

---

## 3. Accounts, APIs, permissions, and integrations required

Set these up in roughly this order — later items often depend on earlier ones (e.g., you can't get Threads API access without an Instagram Professional account first).

1. **Meta Business Manager account** (business.facebook.com) — the umbrella account for everything Meta.
2. **Facebook Page** for the salon, owned by the Business Manager.
3. **Instagram Professional account** (Business or Creator), linked to that Facebook Page.
4. **Meta Developer App** (developers.facebook.com) — created under your Business Manager. This is the app that requests:
   - `instagram_basic`, `instagram_content_publish`, `instagram_manage_comments`, `instagram_manage_insights`
   - `pages_show_list`, `pages_read_engagement`
   - `threads_basic`, `threads_content_publish`, `threads_manage_replies`, `threads_read_replies`
5. **Meta Business verification + App Review** — required to move these permissions from development/test mode to live use. Budget real time for this (historically days to a few weeks); apply early, don't block the rest of the plan on it.
6. **TikTok for Developers account** + registered app, requesting the **Content Posting API**. Unaudited apps can typically only post privately/as drafts — you'll need **app audit approval** for public direct posting. Apply in parallel with the Meta review.
7. **Figma account** (a paid Team plan unlocks shared libraries/component features you'll want for a real brand system) + a **Personal Access Token** or OAuth app for API access.
8. **Canva account** — **Canva for Teams** (needed for Brand Kit + Brand Templates + the Autofill API used to generate on-brand assets programmatically) + a **Canva Developer app** for Connect API access.
9. **Google account** for Calendar API — a Google Cloud project with the Calendar API enabled and OAuth credentials.
10. **Booking platform account** (Fresha/Booksy/Treatwell) — check each platform's current partner/API access terms; some booking platforms restrict API access to certain tiers.
11. **Airtable account** (Team plan recommended once you're relying on it as CRM + content tracker, for permissions/collaboration features).
12. **Google Analytics 4 property** for the website, plus **Meta Pixel** and **TikTok Pixel** installed on the booking page to close the loop between ad spend and bookings.
13. **Supermetrics account**, connected to Meta, TikTok, and GA4 as data sources.
14. **Anthropic API key** (console.anthropic.com) — this is what lets the automation platform (n8n/Make) call the Claude agents programmatically.
15. **n8n account** (n8n Cloud) or self-hosted instance (a small VPS is enough to start).

---

## 4. Connecting the agents into one workflow

Think of it as a **pipeline with a shared data object**, not eight independent chatbots. Each agent reads what the previous one produced and adds to it.

```
Content Brief (JSON/record) — created by Trend Hunter, enriched at every step
 ├─ trend_source
 ├─ concept
 ├─ platform / format
 ├─ captions (DE / UA / EN)
 ├─ hashtags
 ├─ script / hook / shot list / CapCut edit spec
 ├─ status (draft → approved → scripted → filmed → published)
 └─ performance (filled in after publishing)
```

**Orchestration pattern in n8n:**
1. Each agent = one n8n workflow (or one step in a larger workflow) that calls the **Anthropic API**, using the matching `.claude/agents/*.md` file's content as the system prompt, and the current Content Brief as the user message.
2. The agent's structured output (ask Claude to respond in JSON for this layer, even though the `.claude/agents` files are written for conversational use) gets written back into the Content Brief — stored as an **Airtable record**, so every agent reads/writes the same row instead of passing data only through the automation's memory.
3. **Human checkpoints are explicit nodes, not afterthoughts.** Two are non-negotiable at launch:
   - **Approval gate after Creative Director + Social Media Manager**, before scripting — a Slack message with Approve/Reject buttons (n8n supports this natively).
   - **Filming + CapCut editing is always human** — the workflow pauses and waits for a finished video file to appear in a designated Google Drive folder before continuing.
4. Once a finished video lands in that Drive folder, the workflow resumes: publish via the Instagram/TikTok APIs (or queue for manual posting during the MVP phase — see Section 6), then move to monitoring.
5. **Client Communication is not part of the linear chain** — it's a separate, always-on workflow triggered by webhooks (new DM, new comment, new booking-page inquiry), independent of the content pipeline's cadence.
6. **Analytics + Business Analyst run on their own schedule** (nightly pull, monthly report), reading performance data back into the same Content Brief records so results are traceable to the original trend/concept.

---

## 5. The automation flow

```
┌───────────────────┐
│ 1. TREND RESEARCH  │  Scheduled trigger (weekly) → Trend Hunter agent call
│ (Trend Hunter)      │  → Weekly Trend Report → Airtable + Slack
└─────────┬───────────┘
          ▼
┌───────────────────┐
│ 2. CONTENT STRATEGY │  Trend Hunter output → Creative Director agent call
│ (Creative Director + │  → concepts → Social Media Manager agent call
│  Social Media Manager)│ → monthly calendar + trilingual captions + hashtags
└─────────┬───────────┘
          ▼
     [ HUMAN APPROVAL — Slack Approve/Reject ]
          ▼
┌───────────────────┐
│ 3. VIDEO CREATION   │  Approved concept → Reels Video Creator agent call
│ (Reels Video Creator)│ → script + hook options + shot list + CapCut edit spec
└─────────┬───────────┘
          ▼
     [ HUMAN — film + edit in CapCut, drop finished file in Drive folder ]
          ▼
┌───────────────────┐
│ 4. PUBLISHING       │  New file detected → Brand Manager visual check (optional
│                      │  automated pass or human) → publish via IG/TikTok/Threads
│                      │  API at scheduled time → mark Content Brief "published"
└─────────┬───────────┘
          ▼
┌───────────────────┐        ┌──────────────────────────────┐
│ 5. CLIENT           │◀──────│ Webhook: new DM / comment /   │  (always-on, event-driven,
│ COMMUNICATION        │       │ booking inquiry                │   runs independently of
│ (Client Comm. Agent) │        └──────────────────────────────┘   the weekly content cycle)
└─────────┬───────────┘
          ▼
┌───────────────────┐
│ 6. ANALYTICS         │  Nightly: pull IG/TikTok/GA4 data via Supermetrics →
│                      │  attach performance to the matching Content Brief record
└─────────┬───────────┘
          ▼
┌───────────────────┐
│ 7. IMPROVEMENT       │  Monthly: Business Analyst agent call → growth report →
│ (Business Analyst)   │  explicit "what worked" feedback → back into step 1's
│                      │  next Trend Hunter run (adjust prompt/focus areas)
└─────────┴───────────┘
          └──────────────────────────────► loop back to step 1
```

---

## 6. MVP vs. later

The single biggest risk to this project is trying to fully automate publishing before the Meta/TikTok app reviews clear — that can stall the whole launch for weeks. Split the build so **content is already flowing manually while the API approvals are in progress in parallel.**

### MVP (build first — target: running within 1-2 weeks)
- Agent definitions in this repo (already done).
- **Manual operation of the pipeline**: you (or a team member) runs each agent's prompt in Claude Code/Claude Projects in sequence — Trend Hunter → Creative Director → Social Media Manager → Reels Video Creator — no automation platform yet.
- **Airtable** as the single Content Brief tracker + lightweight CRM.
- **Google Calendar** for booking (manual booking flow via DM/website is fine at MVP).
- **Canva + Figma** used directly (manual, not API) for templates and brand assets.
- **CapCut** manual editing (this never changes, even post-MVP).
- **Manual publishing** to Instagram/Threads/TikTok — no API needed yet.
- **Manual weekly analytics logging** into Airtable (5 minutes of copy-pasting numbers from each platform's native insights).
- Start the Meta App Review and TikTok Content Posting API applications **now**, in parallel — they take the longest and gate nothing else in the MVP.

This gets real content published and real client conversations happening immediately, using only the tools you already have.

### Phase 2 — add once MVP is running smoothly (weeks 3-6)
- Stand up **n8n**, migrate the Content Brief from a manually-updated Airtable sheet to an automation-driven one.
- Automate the **scheduled agent calls** (Trend Hunter weekly, Business Analyst monthly) via the Anthropic API.
- Once Meta/TikTok approvals clear: automate **publishing**.
- Add the **Slack approval gate** so review happens without opening Airtable directly.
- Automate **Client Communication Agent** replies via DM webhooks (draft-only at first — a human still hits send).
- Connect **Supermetrics** for automated nightly analytics pulls, replacing manual logging.

### Phase 3 — scale (once the core loop is proven)
- Automatic (not draft-only) client replies for FAQ-tier questions, with escalation rules for anything ambiguous.
- Booking platform upgrade (Fresha/Booksy) if Google Calendar + manual booking starts creating friction (double-bookings, no-shows).
- Marketing Agent-driven **paid ad campaigns** via the Meta Ads API/TikTok Ads Manager, tied to the same Content Brief and measured through the same analytics pipeline.
- Dedicated CRM if Airtable's structure becomes limiting (e.g., you need automated retention/re-engagement sequences at volume).
- A Looker Studio (or similar) dashboard fed by Supermetrics, so the Business Analyst's monthly report becomes a live view instead of a manually-compiled one.

---

## 7. Launch checklist — zero to a working AI marketing system

**Phase 0 — Foundations**
- [ ] Confirm brand specifics (studio name, price list, palette, portfolio) are filled into the relevant agent files (see the README's "A note on brand specifics").
- [ ] Set up Airtable base: one table for the Content Brief pipeline, one for client/CRM records.

**Phase 1 — Accounts (start early, these have the longest lead time)**
- [ ] Meta Business Manager account + Facebook Page + Instagram Professional account.
- [ ] Meta Developer App created; submit for App Review (Instagram + Threads permissions).
- [ ] TikTok for Developers account + app; apply for Content Posting API audit.
- [ ] Canva for Teams account; Canva Developer app registered.
- [ ] Figma Team plan; personal access token generated.
- [ ] Google Cloud project with Calendar API enabled.
- [ ] Booking platform account chosen (Fresha/Booksy/Treatwell) if going beyond bare Google Calendar.
- [ ] GA4 property created for the website; Meta + TikTok pixels installed on the booking page.
- [ ] Supermetrics account, connected to Meta/TikTok/GA4.
- [ ] Anthropic API key generated.

**Phase 2 — MVP content engine (manual, no automation platform yet)**
- [ ] Run Trend Hunter manually → produce first Weekly Trend Report.
- [ ] Run Creative Director on that report → first batch of concepts.
- [ ] Run Social Media Manager → first monthly calendar + trilingual captions + hashtags.
- [ ] Run Reels Video Creator on the first approved concept → script + hook + shot list + CapCut spec.
- [ ] Film and edit the first video in CapCut by hand.
- [ ] Brand Manager visual pass before publishing (manual review against brand guidelines).
- [ ] Publish manually to Instagram/TikTok/Threads.
- [ ] Client Communication Agent used manually to draft replies to the first resulting DMs/comments.
- [ ] Log first week's performance numbers into Airtable manually.
- [ ] Run Business Analyst at the end of month one on that manually-logged data → first growth report.

**Phase 3 — Automation layer**
- [ ] Stand up n8n (cloud or self-hosted).
- [ ] Build the Trend Hunter scheduled workflow (weekly trigger → Anthropic API call → Airtable write → Slack notification).
- [ ] Build the Creative Director + Social Media Manager workflow, ending in a Slack approval gate.
- [ ] Build the Reels Video Creator workflow, ending in a "waiting for finished video" Drive-folder watcher.
- [ ] Once Meta/TikTok API approvals are live: build the publishing workflow.
- [ ] Build the Client Communication webhook workflow (draft-only replies to start).
- [ ] Build the nightly Supermetrics → Airtable analytics pull.
- [ ] Build the monthly Business Analyst workflow, closing the loop back into the next Trend Hunter run.

**Phase 4 — Go live and iterate**
- [ ] Run one full automated cycle end to end with a human checking every checkpoint.
- [ ] Turn on auto-publishing once you trust the approval gate.
- [ ] Turn on auto-send for Client Communication only after a few weeks of reviewing its draft-only replies.
- [ ] Review the first automated Business Analyst report and adjust Trend Hunter/Creative Director prompts based on what it says actually converted.
