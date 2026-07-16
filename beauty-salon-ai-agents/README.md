# Beauty Salon AI Agent Team

A ready-to-use team of 7 specialized [Claude Code subagents](https://docs.claude.com/en/docs/claude-code/sub-agents) for running a beauty salon's marketing, client, and business operations. The agent definitions live in [`.claude/agents/`](../.claude/agents) at the repo root and are automatically available in any Claude Code session opened on this repository.

## The team

| Agent | File | Core job |
|---|---|---|
| Social Media Manager | [`social-media-manager.md`](../.claude/agents/social-media-manager.md) | Instagram/Threads/TikTok content calendars, reels ideas, captions, hashtags, trend analysis |
| Content Creator | [`content-creator.md`](../.claude/agents/content-creator.md) | Post copy, video scripts, hooks, story ideas, beauty content strategy |
| Video Editor | [`video-editor.md`](../.claude/agents/video-editor.md) | CapCut edit plans, reels editing ideas, video structure, subtitles, short-form optimization |
| Marketing Agent | [`marketing-strategist.md`](../.claude/agents/marketing-strategist.md) | Advertising strategy, customer acquisition, promotions, sales funnels |
| Client Assistant | [`client-assistant.md`](../.claude/agents/client-assistant.md) | Customer communication, appointment management, FAQs, booking support |
| Brand Design | [`brand-designer.md`](../.claude/agents/brand-designer.md) | Figma/Canva design system, visual identity, Instagram aesthetics |
| Business Analyst | [`business-analyst.md`](../.claude/agents/business-analyst.md) | Revenue tracking, customer retention, growth analysis |

## How the team works together

```
Trend research ──▶ Social Media Manager ──▶ Content Creator ──▶ Video Editor ──▶ (published post)
                          │                        │                  │
                          ▼                        ▼                  ▼
                    Brand Design (templates/graphics for every agent above)

Marketing Agent ──▶ campaigns/promotions ──▶ Client Assistant (bookings, FAQs, follow-up)
                          │                        │
                          ▼                        ▼
                    Business Analyst ◀── revenue, retention, and campaign data from both
```

- **Social Media Manager** sets the content calendar and strategy.
- **Content Creator** writes the actual scripts/captions for each calendar slot.
- **Video Editor** turns scripts into shot-by-shot, CapCut-ready edit plans.
- **Brand Design** supplies the on-brand templates/graphics every other content agent uses.
- **Marketing Agent** designs the campaigns and funnels that turn that content into bookings.
- **Client Assistant** handles the resulting client conversations and appointments.
- **Business Analyst** closes the loop by reporting what's actually working, feeding back into the Social Media Manager's and Marketing Agent's next plan.

## Skills, workflows, and integrations at a glance

| Agent | Key skills | Integrations used |
|---|---|---|
| Social Media Manager | Content calendars, reels ideas, captions, hashtags, trends | WebSearch/WebFetch, Canva, Notion/Airtable, Slack |
| Content Creator | Post drafting, scripts, hooks, story ideas, content strategy | WebSearch/WebFetch, Notion, Canva, Google Drive |
| Video Editor | CapCut edit plans, reel structure, subtitles, short-form optimization | Descript, HyperFrames by HeyGen, Google Drive |
| Marketing Agent | Ad strategy, acquisition, promotions, funnels | Resend, Supermetrics, Airtable, WebSearch |
| Client Assistant | Client messaging, scheduling, FAQs, booking support | Google Calendar, Gmail, Resend, Slack |
| Brand Design | Design systems, templates, visual identity, grid planning | Figma, Canva, Google Drive |
| Business Analyst | Revenue, retention, growth reporting | Airtable, Supermetrics, WebSearch |

## Using the agents

Once this repo is open in Claude Code, each agent is invokable by name — e.g. ask for "the social media manager to plan next week's content" or "have the business analyst pull a retention report," and Claude Code will route to the matching subagent in `.claude/agents/`. Agents can also be chained manually (e.g. Social Media Manager → Content Creator → Video Editor) for a full content pipeline in one request.

To connect an agent to a live tool (e.g. the real Instagram/TikTok account, the salon's actual booking calendar, or its CRM), wire up the corresponding MCP connector for your workspace — the integrations listed above are the ones each agent is already instructed to use once available.

## Customizing

Each agent file is a plain Markdown file with a YAML frontmatter header (`name`, `description`) followed by its system prompt. Edit any file in `.claude/agents/` directly to adjust tone, add real brand details (palette, service menu, pricing, policies), or change which tools/integrations an agent should reach for.
