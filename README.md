# Wrench.ai Claude Code Plugins

> **Official plugin marketplace from [Wrench.ai](https://wrench.ai)** — Claude Code skills for B2B lead intelligence, revenue operations, feature specs, data engineering review, SEO audits, and outreach sequences. 11 production-ready skills, one `/plugin` command away.

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
/plugin install meeting-dossier@wrench-plugins
/plugin install bug-audit@wrench-plugins
/plugin install seo-audit@wrench-plugins
/plugin install page-cro@wrench-plugins
/plugin install wrench-de-review@wrench-plugins
/plugin install wrench-ux-constitution@wrench-plugins
/plugin install dossier-builder@wrench-plugins
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

### Data Visualization

| Skill | Trigger | What it does |
|---|---|---|
| **`wrench-charts`** | `build a chart`, `add a chart`, `data visualization` | Brand-compliant Recharts components — chart type selection guide, color tokens, custom tooltips, mobile responsiveness. Never writes a chart without correct tokens and layout. |

### Engineering Quality

| Skill | Trigger | What it does |
|---|---|---|
| **`feature-spec`** | `write a spec`, `spec this out`, `write a PRD` | Complete feature spec with acceptance criteria, task breakdown, and Cypress test plan. Missing any of the three = incomplete spec. |
| **`bug-audit`** | `bug audit`, `audit before release`, `QA this feature` | Structured audit covering functional bugs, UX bugs, test coverage gaps, and customer-surfaced classification. Produces HOLD / SHIP WITH KNOWN ISSUES / CLEAR TO SHIP. |
| **`wrench-de-review`** | `review this PR`, `data engineering review` | Data engineering PR review — idempotency, dedup logic, cardinality assertions, schema contracts, error handling, and automation completeness. |
| **`wrench-ux-constitution`** | `does this pass the constitution`, `run the checklist` | UX governance skill — 11 design principles, Six Pillars initiative, Seven Empirical Gaps, and a pre-ship checklist. Settles design disagreements. |

### Sales & Intelligence

| Skill | Trigger | What it does |
|---|---|---|
| **`wrench-personalize`** | `personalize this`, `persona-based messaging`, `segment this audience` | Full persona-based personalization framework — archetype identification, meta-measure stack, positioning layer, and ready-to-deploy message variants. Includes worked example using the military/veteran buyer segment. Works standalone; scales with live Wrench.ai behavioral data. |
| **`wrench-dossier`** | `dossier for [name]`, `brief on [name]`, `prep me on [person]` | Contact intelligence brief plus voice-adaptive message drafts. Paste in anything — LinkedIn bio, job title, company — and get a scannable brief plus outreach ready to send. Adapts to sender voice and audience type. Works standalone or Wrench.ai-powered. |
| **`wrench-report`** | `competitive report`, `brand insights`, `lead scoring report`, `CRM report` | Sales intelligence reports in three modes: Competitive 360 (competitor positioning + battle cards), Brand Insights (persona resonance), or Lead Scoring CRM Report (ranked pipeline + next actions). HTML output. Works standalone with pasted data. |
| **`wrench-outreach`** | `write outreach`, `cold email`, `LinkedIn sequence` | Multi-step B2B outreach sequences — email and LinkedIn, persona-specific, non-corporate voice. Subject lines, body copy, timing. |
| **`meeting-dossier`** | `dossier for [name]`, `prep me on [person]` | Pre-meeting intelligence brief using live Wrench.ai lead score and enrichment data — personas, talking points, draft messages, behavioral predictions. |
| **`dossier-builder`** | `build a dossier for [name]`, `generate intel on [name]` | Contact intelligence dossier — pulls Wrench.ai signals and renders a scannable HTML artifact. Recommendations first, evidence second. |

### Content & Marketing

| Skill | Trigger | What it does |
|---|---|---|
| **`humanizer`** | `humanize this`, `make it sound less robotic`, `this sounds like AI` | Removes AI writing patterns — strips hedging, filler phrases, corporate-speak, and statistical-middle phrasing. Makes text specific and direct. |
| **`seo-audit`** | `SEO audit`, `why am I not ranking`, `technical SEO` | Comprehensive SEO audit — technical issues, on-page optimization, Core Web Vitals, crawl errors, indexing, and structured data. |
| **`page-cro`** | `CRO`, `this page isn't converting`, `improve my landing page` | Conversion rate optimization for marketing pages — above the fold, CTA clarity, social proof, and bounce rate analysis. |

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
