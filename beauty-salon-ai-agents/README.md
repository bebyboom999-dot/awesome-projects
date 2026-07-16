# AI Agent Team — Premium Hair Colorist Personal Brand (Austria)

A ready-to-use team of 8 specialized [Claude Code subagents](https://docs.claude.com/en/docs/claude-code/sub-agents) built for one specific goal: **build a premium personal brand for a professional hair colorist in Austria and convert social media attention into real client bookings.** The agent definitions live in [`.claude/agents/`](../.claude/agents) at the repo root and are automatically available in any Claude Code session opened on this repository.

For the step-by-step technical plan to actually run this system end to end — platform choice, tool/API integrations, orchestration, automation flow, and a launch checklist — see [`IMPLEMENTATION_PLAN.md`](IMPLEMENTATION_PLAN.md).

This is not a generic beauty-salon content team. Every agent is scoped to:
- **Positioning**: a premium, personal-brand hair colorist — not a generic salon account.
- **Expertise**: professional color work (color correction, technique precision, formulation) as the core content pillar, not generic beauty tips.
- **Market**: Vienna/Austria, trilingual audience — German-speaking locals, the Ukrainian community, and English-speaking expats.
- **Business goal**: bookings, not vanity metrics — every agent's output is expected to trace back to attention → trust → booking.

## The team

| Agent | File | Core job |
|---|---|---|
| AI Viral Trend Hunter | [`trend-hunter.md`](../.claude/agents/trend-hunter.md) | Instagram/TikTok/Threads trend scanning, hair color & fashion trend monitoring, competitor analysis, weekly trend reports |
| AI Creative Director | [`creative-director.md`](../.claude/agents/creative-director.md) | Unique on-brand content concepts, storytelling, turning colorist expertise into emotional/educational content |
| AI Social Media Manager | [`social-media-manager.md`](../.claude/agents/social-media-manager.md) | Monthly content calendars, platform strategy, trilingual (UA/DE/EN) captions, local-Austria hashtags |
| AI Reels Video Creator | [`reels-video-creator.md`](../.claude/agents/reels-video-creator.md) | Video scripts, 3-second hooks, shot lists, text overlays, CapCut editing style |
| AI Beauty Marketing Agent | [`marketing-strategist.md`](../.claude/agents/marketing-strategist.md) | Campaigns, offers, customer psychology, sales funnels, conversion-focused advertising |
| AI Client Manager & Business Optimization Agent | [`client-manager.md`](../.claude/agents/client-manager.md) | Client messaging, structured consultation, qualification, booking, feedback surveys, communication-process analysis, marketing feedback, time-optimization reports — the virtual salon administrator |
| AI Brand Manager | [`brand-designer.md`](../.claude/agents/brand-designer.md) | Figma/Canva design system, Instagram aesthetics, premium visual identity |
| AI Business Analyst | [`business-analyst.md`](../.claude/agents/business-analyst.md) | Content performance, client-source attribution, retention, monthly growth reports |

## The workflow: trends → strategy → production → conversion → analysis → repeat

```
 1. TREND HUNTER              2. CREATIVE DIRECTOR          3. SOCIAL MEDIA MANAGER + REELS VIDEO CREATOR
 Weekly trend report   ─────▶ Filters trends through  ────▶ Calendar, trilingual captions, hashtags,
 (Emerging/Peaking/           brand + expertise into        scripts, hooks, shot lists, CapCut edit style
 Saturated, hair-color-       storytelling concepts               │
 specific, competitor         (rejects generic ideas)              ▼
 read)                                                        (content is filmed & published)
                                                                     │
                                                                     ▼
 6. BUSINESS ANALYST   ◀───── 5. CLIENT MANAGER        ◀──── 4. BEAUTY MARKETING AGENT
 Monthly growth report,       Handles resulting DMs,         Campaigns/offers/funnels that turn
 attribution, retention,      consultation, qualification,   attention into booked appointments
 feeds next cycle             booking, in DE/UA/EN           (trust-led, not discount-led)
        │                            │
        │                            └──▶ communication-process + marketing-feedback reports
        │                                 feed Creative Director/Trend Hunter (content gaps)
        │                                 and Marketing Agent (what actually converts)
        └──────────────────▶ back into Trend Hunter + Creative Director for the next cycle
```

Cutting across all of it: the **AI Brand Manager** reviews everything the Creative Director, Social Media Manager, Reels Video Creator, and Marketing Agent produce, to keep the visual identity premium and consistent — it's a horizontal quality gate, not a step in the linear pipeline.

- **Trend Hunter** always runs first — no content gets planned on stale or saturated trends.
- **Creative Director** is the filter: nothing generic survives to reach the calendar.
- **Social Media Manager** and **Reels Video Creator** turn approved concepts into publishable captions and shootable scripts, respectively.
- **Marketing Agent** exists specifically to convert the resulting attention into consultations and bookings, using trust-led (not discount-led) funnels appropriate to a high-ticket, high-trust service.
- **Client Manager & Business Optimization Agent** is the virtual salon administrator: it handles the real conversations that follow (in the client's own language), runs structured consultations, qualifies and preps clients before the colorist is involved, and produces its own communication-process, conversion, and time-optimization reports — feeding content gaps back to Creative Director/Trend Hunter and conversion signal to the Marketing Agent.
- **Business Analyst** closes the loop — reporting not just "what happened" but which trend bets and which storytelling registers actually converted, rolling in the Client Manager's communication data, and feeding directly into the next Trend Hunter/Creative Director cycle.

## Skills, workflows, and integrations at a glance

| Agent | Key skills | Integrations used |
|---|---|---|
| Trend Hunter | Platform trend scanning, hair color/fashion trend tracking, competitor & creator analysis, weekly reports | Instagram, TikTok, Threads, WebSearch/WebFetch, Notion/Airtable |
| Creative Director | Concept development, storytelling, expertise-to-content translation, emotional/educational balance | Notion, WebSearch, Canva |
| Social Media Manager | Monthly calendars, platform strategy, trilingual captions, local hashtags | Instagram, Threads, TikTok, Notion/Airtable, Canva, WebSearch |
| Reels Video Creator | Scripts, hooks, shot lists, text overlays, CapCut edit specs | CapCut, Descript, HyperFrames by HeyGen, Google Drive |
| Beauty Marketing Agent | Campaigns, customer psychology, funnels, advertising strategy | CRM, Google Calendar, Analytics/Supermetrics, Instagram, TikTok, WebSearch |
| Client Manager & Business Optimization Agent | Trilingual messaging, structured consultation, qualification, booking, feedback surveys, communication-process analysis, marketing feedback, time-optimization reporting | Instagram, website chat, Google Calendar, the existing CRM/client database, Gmail, Slack |
| Brand Manager | Visual identity, design system, grid aesthetics | Figma, Canva, Google Drive |
| Business Analyst | Performance attribution, retention analysis, growth reporting | CRM, Analytics/Supermetrics, Google Calendar, WebSearch |

## Required integrations — status

| Integration | Used by | Status in this workspace |
|---|---|---|
| Instagram | Trend Hunter, Social Media Manager, Marketing Agent | Not yet connected — wire up an Instagram connector to enable direct trend/posting/ad access |
| Threads | Trend Hunter, Social Media Manager | Not yet connected |
| TikTok | Trend Hunter, Social Media Manager, Marketing Agent | Not yet connected |
| CapCut | Reels Video Creator | Not yet connected — agent produces CapCut-ready edit specs today; direct CapCut automation requires a connector |
| Canva | Creative Director, Social Media Manager, Brand Manager | **Live** (`mcp__Canva__*`) |
| Figma | Brand Manager | **Live** (`mcp__Figma__*`) |
| Google Calendar | Client Manager Agent, Marketing Agent, Business Analyst | **Live** (`mcp__Google_Calendar__*`) |
| CRM / client database | Marketing Agent, Client Manager Agent, Business Analyst | **Use the existing client database already in place — do not stand up a new one.** Once it's clear which system that is (Airtable, a booking platform's built-in client list, a spreadsheet, etc.), connect the matching connector here so agents read/write it directly instead of working from copy-pasted exports |
| Analytics tools | Marketing Agent, Business Analyst | **Live** via Supermetrics Marketing Analytics (`mcp__Supermetrics_Marketing_Analytics__*`) |

Agents are written to use each integration as soon as it's available; where a connector isn't wired up yet, the agent still produces the full deliverable (report, script, campaign brief) ready to execute manually or paste into the target tool.

## Using the agents

Once this repo is open in Claude Code, each agent is invokable by name — e.g. "have the trend hunter pull this week's report," "creative director, turn this client story into a concept," "reels video creator, script this," "client manager, draft this week's communication report." Chain them in pipeline order (Trend Hunter → Creative Director → Social Media Manager → Reels Video Creator → Marketing Agent → Client Manager → Business Analyst) for a full cycle in one request, or invoke any single agent standalone. The Client Manager Agent also runs independently, any time, for incoming client messages — it isn't gated on the rest of the pipeline.

## A note on brand specifics

These agents encode the **positioning** (premium personal brand, professional hair colorist, Austria market, trilingual DE/UA/EN, conversion-over-vanity-metrics) discussed in this session. They do not yet contain a saved brand-discovery document with exact studio name, price list, color palette hex codes, or portfolio links — drop those specifics into the relevant agent file (mainly Brand Manager for visual specifics, Client Manager for FAQ/policy specifics) and every downstream agent will use them automatically. Likewise, tell the Client Manager Agent directly which system your existing client database lives in — it's built to use what's already there, not to replace it.

## Customizing

Each agent file is a plain Markdown file with a YAML frontmatter header (`name`, `description`) followed by its system prompt. Edit any file in `.claude/agents/` directly to add real brand details, adjust tone, or change which integrations an agent should reach for.
