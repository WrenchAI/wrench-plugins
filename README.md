# Wrench.ai Claude Code Plugins

> **Official plugin marketplace from [Wrench.ai](https://wrench.ai)** — Claude Code skills for B2B sales intelligence, outreach, and revenue operations. Organized around three questions: **WHO** do I talk to today? **WHAT** do I say? **WHY** did the AI decide that? 22 production-ready skills, one `/plugin` command away.

[![Install in Claude Code](https://img.shields.io/badge/Install%20in%20Claude%20Code-%2F plugin%20marketplace%20add-5E80A8?style=flat-square)](https://code.claude.com/docs/en/discover-plugins)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](./LICENSE)
[![Smithery](https://img.shields.io/badge/Listed%20on-Smithery-9EEFC7?style=flat-square)](https://smithery.ai/server/wrench-plugins)

---

## What is this?

This is the official Claude Code plugin marketplace from [Wrench.ai](https://wrench.ai) — a B2B intelligence and personalization platform that helps revenue teams understand their buyers through predictive lead scoring, persona matching, behavioral enrichment, and AI-powered outreach.

These plugins bring Wrench.ai's methodology — direct, data-driven, and outcome-focused — to Claude Code as installable skills. Each skill is production-tested and synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna), Wrench.ai's internal standards repository.

**Who is this for?**
- **Sales and RevOps teams** using Claude Code for prospecting, deal analysis, and outreach
- **Engineers** who want structured spec writing, bug auditing, and data engineering review
- **Marketers** building SEO audits and conversion optimization workflows
- **Anyone** using Claude Code who wants to remove AI writing patterns from their copy

---

## Install

### One-line setup

```
/plugin marketplace add github:WrenchAI/wrench-plugins
```

### Install individual skills

```
/plugin install wrench-personalize@wrench-plugins
/plugin install wrench-dossier@wrench-plugins
/plugin install wrench-report@wrench-plugins
/plugin install wrench-charts@wrench-plugins
/plugin install feature-spec@wrench-plugins
/plugin install humanizer@wrench-plugins
/plugin install wrench-outreach@wrench-plugins
/plugin install wrench-daily-brief@wrench-plugins
/plugin install wrench-call-log@wrench-plugins
/plugin install wrench-quick-response@wrench-plugins
/plugin install bug-audit@wrench-plugins
/plugin install seo-audit@wrench-plugins
/plugin install page-cro@wrench-plugins
/plugin install wrench-de-review@wrench-plugins
/plugin install wrench-ux-constitution@wrench-plugins
/plugin install dossier-builder@wrench-plugins
/plugin install deal-strategist@wrench-plugins
/plugin install passive-deal-updater@wrench-plugins
/plugin install wrench-deal-estimator@wrench-plugins
/plugin install workspace-onboarding@wrench-plugins
/plugin install content-template-builder@wrench-plugins
```

### Auto-install for your whole team

Add to your project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    {
      "name": "wrench-plugins",
      "source": {
        "source": "github",
        "repo": "WrenchAI/wrench-plugins"
      }
    }
  ]
}
```

Then any team member can run `/plugin install <name>@wrench-plugins`.

---

## Available Skills

The sales skills are organized around three questions every rep needs answered on every deal:

**WHO** → who is this buyer, and who do I contact today?  
**WHAT** → what do I say, and what happened on the call?  
**WHY** → why did I prioritize this way — show me the data behind the decision.

---

### WHO — Know Your Buyer

> These skills answer: "Who am I talking to, and who should I focus on today?"

| Skill | Trigger | What it does |
|---|---|---|
| **`wrench-daily-brief`** | `who should I call today`, `daily brief`, `morning brief`, `who's hot today` | Daily sales prioritization queue — surfaces who to call today, ranked by signal strength, with a why-today reason and opening line for each contact. Standalone or signal-driven with Wrench.ai. |
| **`wrench-dossier`** | `dossier for [name]`, `brief on [name]`, `prep me on [person]` | Contact intelligence brief plus voice-adaptive message drafts. Paste in anything — LinkedIn bio, job title, company — and get a scannable brief plus outreach ready to send. Adapts to sender voice and audience type. Works standalone or Wrench.ai-powered. |
| **`wrench-personalize`** | `personalize this`, `persona-based messaging`, `segment this audience` | Full persona-based personalization framework — archetype identification, behavioral signal stack, positioning layer, and ready-to-deploy message variants. Works standalone; scales with live Wrench.ai data. |

---

### WHAT — Know What to Say

> These skills answer: "What do I write, what do I say, and what just happened on that call?"

| Skill | Trigger | What it does |
|---|---|---|
| **`wrench-outreach`** | `write outreach`, `cold email`, `LinkedIn sequence`, `write a message to` | B2B outreach writer — instant single messages or full multi-step sequences, email and LinkedIn. Quick Message mode for one draft in seconds; Sequence mode for full campaigns. With Wrench.ai: hook angle from live behavioral data. |
| **`wrench-quick-response`** | `book a meeting with`, `introduce [name]`, `ask for a review`, `checking in with` | Conversion-optimized drafts for high-stakes B2B micro-interactions — meeting requests, warm intros, review asks, referral asks, relationship check-ins, and post-meeting thank you notes. Each response type has a specific conversion principle built in. |
| **`wrench-call-log`** | `log this call`, `call summary`, `I just finished a call with`, `update CRM` | Post-call CRM logging and follow-up writer. Accepts rough notes, bullets, or a voice transcript and produces structured CRM-ready fields, key signals, next action, and a ready-to-send follow-up. With Wrench.ai: pushes outcome to the contact record and calibrates follow-up to behavioral signals. |
| **`deal-strategist`** | `what's the next step for`, `what should I send`, `prep for my call`, `which playbook` | Selects the right playbook, recommends the next action (with timing, angle, and rationale), surfaces proof assets, and drafts personalized outreach calibrated to contact archetype. Works from pasted notes or live CRM data. |
| **`wrench-deal-estimator`** | `estimate this deal`, `what would this cost`, `put together a quote`, `scope this` | Deal scoping and pricing estimator — selects the right pricing model, anchors on value before cost, builds a line-item range, and produces a proposal-ready investment rationale. With Wrench.ai: uses buyer intent score to set pricing posture. |
| **`content-template-builder`** | `create a template`, `build an email template`, `make a template for`, `make a template from this` | Builds a MASTER archetype-neutral base template plus up to 5 archetype-calibrated variants (email and LinkedIn) for any deal stage and vertical. Includes personalization checklist and archetype routing guide. |

---

### WHY — Show Your Work

> These skills answer: "Why did the AI prioritize this way — what data drove the decision?"

Every skill output ends with a WHY block that translates the data reasoning into plain language. The `wrench-report` skill surfaces that evidence as a shareable report.

| Skill | Trigger | What it does |
|---|---|---|
| **`wrench-report`** | `competitive report`, `brand insights`, `lead scoring report`, `CRM report` | Sales intelligence reports in three modes: Competitive 360 (competitor positioning + battle cards), Brand Insights (persona resonance), or Lead Scoring CRM Report (ranked pipeline + next actions). HTML output. Works standalone with pasted data. |

---

### Deal Operations & Automation

| Skill | Trigger | What it does |
|---|---|---|
| **`passive-deal-updater`** | `build the deal updater`, `update my deals automatically`, `nightly deal intelligence`, `passive CRM update` | Builds an n8n automation that nightly pulls signals from email, calendar, Slack, and CRM activity, synthesizes them with AI, and writes structured deal notes + next steps back to your CRM — so your pipeline reflects reality without manual data entry. |

---

### Platform

| Skill | Trigger | What it does |
|---|---|---|
| **`workspace-onboarding`** | `set up the workspace`, `populate the workspace`, `get the workspace ready`, `workspace setup` | Fully populates a new Wrench.ai workspace before a client's first login — competitors, AI ingredients (meta-measures), seed contacts, AI creatives, and workspace agent — so every screen shows relevant data on day one. Calls `wrench-report` and `wrench-metameasure-create` as sub-skills. |

---

### Engineering & Quality

| Skill | Trigger | What it does |
|---|---|---|
| **`feature-spec`** | `write a spec`, `spec this out`, `write a PRD` | Complete feature spec with acceptance criteria, task breakdown, and Cypress test plan. Missing any of the three = incomplete spec. |
| **`bug-audit`** | `bug audit`, `audit before release`, `QA this feature` | Structured audit covering functional bugs, UX bugs, test coverage gaps, and customer-surfaced classification. Produces HOLD / SHIP WITH KNOWN ISSUES / CLEAR TO SHIP. |
| **`wrench-de-review`** | `review this PR`, `data engineering review` | Data engineering PR review — idempotency, dedup logic, cardinality assertions, schema contracts, error handling, and automation completeness. |
| **`wrench-ux-constitution`** | `does this pass the constitution`, `run the checklist` | UX governance skill — 11 design principles, Six Pillars initiative, Seven Empirical Gaps, and a pre-ship checklist. Settles design disagreements. |
| **`wrench-charts`** | `build a chart`, `add a chart`, `data visualization` | Brand-compliant Recharts components — chart type selection guide, color tokens, custom tooltips, mobile responsiveness. Never writes a chart without correct tokens and layout. |

---

### Content & Marketing

| Skill | Trigger | What it does |
|---|---|---|
| **`humanizer`** | `humanize this`, `make it sound less robotic`, `this sounds like AI` | Removes AI writing patterns — strips hedging, filler phrases, corporate-speak, and statistical-middle phrasing. Makes text specific and direct. |
| **`seo-audit`** | `SEO audit`, `why am I not ranking`, `technical SEO` | Comprehensive SEO audit — technical issues, on-page optimization, Core Web Vitals, crawl errors, indexing, and structured data. |
| **`page-cro`** | `CRO`, `this page isn't converting`, `improve my landing page` | Conversion rate optimization for marketing pages — above the fold, CTA clarity, social proof, and bounce rate analysis. |

---

### Legacy / Deprecated

| Skill | Status |
|---|---|
| **`meeting-dossier`** | ⚠️ Deprecated — use `wrench-dossier` instead. It covers everything this skill does plus standalone mode and voice-adaptive drafts. |
| **`dossier-builder`** | Requires an active Wrench.ai workspace and internal MCP setup. Not for general use — use `wrench-dossier` for contact intelligence. |

---

## Connect Claude to the Wrench.ai MCP Server

The skills above work standalone. For the intelligence skills (`wrench-personalize`, `wrench-dossier`, `wrench-report`, `meeting-dossier`, `dossier-builder`) to pull live data — lead scores, personas, enrichment, competitor intelligence — you need to connect your Claude instance to your Wrench.ai workspace via MCP.

> **Full setup guide:** [docs/setup-mcp.md](./docs/setup-mcp.md) — covers Claude Code CLI, Claude Desktop app (macOS + Windows), Claude.ai Cowork/Chat, VS Code, JetBrains, Cursor, and Windsurf.

### What is the Wrench.ai MCP?

The Wrench.ai MCP (Model Context Protocol) server gives Claude direct access to your workspace data: lead scores, contact enrichment, persona distributions, campaign analytics, competitor intelligence, and more. The MCP is what turns Claude into a revenue co-pilot rather than a generic assistant.

### Quick setup — Claude Code (one command)

```bash
claude mcp add --transport sse wrench https://api.v2.wrench.ai/mcp/sse \
  --header "x-api-key: YOUR_WRENCH_API_KEY"
```

Get your key at [web.wrench.ai/api-key](https://web.wrench.ai/api-key). Then verify with `/mcp` inside Claude Code.

### Quick setup — Claude Desktop app

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "wrench": {
      "type": "sse",
      "url": "https://api.v2.wrench.ai/mcp/sse",
      "headers": {
        "x-api-key": "YOUR_WRENCH_API_KEY"
      }
    }
  }
}
```

Quit and relaunch Claude Desktop after saving.

### Quick setup — Claude.ai Cowork / Chat (web)

Settings → Integrations → MCP Servers → Add server:
- **URL:** `https://api.v2.wrench.ai/mcp/sse`
- **Header:** `x-api-key: YOUR_WRENCH_API_KEY`

See [docs/setup-mcp.md](./docs/setup-mcp.md) for Cursor, Windsurf, VS Code, and JetBrains instructions.

---

## About Wrench.ai

[Wrench.ai](https://wrench.ai) is a B2B intelligence and personalization platform. We turn raw contact and company data into actionable intelligence — predictive lead scores, persona archetypes, behavioral affinities, and personalized outreach — so every business relationship is smarter.

**Measured outcomes:**
- **183% greater lead scoring accuracy** than traditional CRM scores
- **Up to 10×** improvement in acquisition over traditional contact lists
- **12.5–25% SDR productivity improvement** at net-zero cost
- **1–2 months of additional revenue** captured in the first 12 months

**Core capabilities:** Contact and company enrichment · Predictive lead scoring · Persona and archetype matching · AI-powered message composition · 110+ CRM, eCommerce, and analytics integrations

---

## Frequently Asked Questions

**Do I need a Wrench.ai account to use these plugins?**

Most plugins work without a Wrench.ai account — `feature-spec`, `humanizer`, `bug-audit`, `wrench-charts`, `seo-audit`, `page-cro`, `wrench-de-review`, `wrench-ux-constitution`, `wrench-outreach`, `wrench-personalize`, `wrench-dossier`, and `wrench-report` are fully standalone. The standalone skills deliver real value from any data you provide. With a Wrench.ai workspace connected via MCP, `wrench-personalize`, `wrench-dossier`, and `wrench-report` replace inferred signals with live behavioral scores, persona distributions, competitor data, and lead scores — automatically. `meeting-dossier` and `dossier-builder` require a Wrench.ai workspace to function.

**How do I update plugins when new versions are released?**

```
/plugin marketplace update wrench-plugins
```

**Can I use these skills in Claude.ai (not Claude Code)?**

Yes. Copy the `SKILL.md` content from any plugin's `skills/<name>/SKILL.md` file and paste it into a Claude.ai Project instruction. The skills are written as plain Markdown and work in any Claude context.

**Are these skills safe? Can they modify my files?**

Skills are instructions, not code that runs automatically. They guide Claude's behavior during a session. The `meeting-dossier` and `dossier-builder` skills make read-only calls to the Wrench.ai API when connected via MCP. No skill writes files or executes commands without explicit user instruction.

**How are these skills kept up to date?**

Skills are synced from [wrench-dna](https://github.com/WrenchAI/wrench-dna), Wrench.ai's internal standards repository. When skills are updated there, they're automatically synced here via `scripts/sync-plugins.sh` in wrench-dna.

---

## Listed On

| Registry | Link |
|---|---|
| Official MCP Registry | [registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io) |
| Smithery | [smithery.ai/server/wrench-plugins](https://smithery.ai/server/wrench-plugins) |
| Glama | [glama.ai/mcp/servers](https://glama.ai/mcp/servers) |
| mcp.so | [mcp.so](https://mcp.so) |
| anthropics/claude-plugins-official | Under review |

---

## License

MIT — see [LICENSE](./LICENSE)

---

*Maintained by [Wrench.ai](https://wrench.ai) · [Get a workspace](https://web.wrench.ai) · [API docs](https://api.v2.wrench.ai/docs)*
