# Changelog

All notable changes to this marketplace are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/):
- **Patch** (1.0.x) — skill content updates, bug fixes, documentation corrections
- **Minor** (1.x.0) — new skills added, new docs, new features
- **Major** (x.0.0) — removed skills, renamed plugins, breaking schema changes

---

## [1.1.0] — 2026-05-01

### Added
- **`wrench-personalize`** — Persona-based B2B personalization framework. Archetype
  identification, meta-measure stack, positioning layer, and ready-to-deploy message
  variants. Includes complete worked example using the military/veteran buyer segment.
  Works standalone; scales with live Wrench.ai behavioral data.
- **`wrench-dossier`** — Contact intelligence brief with voice-adaptive ghostwriter
  output. Paste any contact data — LinkedIn bio, job title, company — and get a
  scannable brief plus outreach ready to send. Captures sender voice style and adapts
  to audience type (C-suite, champion, practitioner).
- **`wrench-report`** — Sales intelligence reports with three modes: Competitive 360
  (competitor positioning and per-competitor battle cards), Brand Insights (persona
  resonance and message-market fit analysis), or Lead Scoring CRM Report (ranked
  pipeline with tier badges and per-contact next actions). HTML artifact output.
- **`docs/setup-mcp.md`** — Comprehensive MCP setup guide covering Claude Code CLI,
  Claude Desktop (macOS + Windows), Claude.ai Cowork/Chat, VS Code, JetBrains,
  Cursor, and Windsurf.

### Changed
- README updated: new skills listed, install commands expanded to 14, MCP connect
  section updated to reference new intelligence skills, FAQ updated.
- `llms.txt` updated to 14 skills.

---

## [1.0.0] — 2026-04-29

### Added
- Initial marketplace release with 11 production-ready skills.
- **`wrench-charts`** — Brand-compliant Recharts components for React — chart type
  selection guide, color tokens, custom tooltips, mobile responsiveness.
- **`feature-spec`** — Complete feature specs with acceptance criteria, task
  breakdown, and Cypress test plan.
- **`bug-audit`** — Structured release audit covering functional bugs, UX bugs,
  test coverage gaps, and customer-surfaced issues. Produces HOLD / SHIP WITH
  KNOWN ISSUES / CLEAR TO SHIP verdict.
- **`wrench-de-review`** — Data engineering PR review — idempotency, dedup logic,
  cardinality assertions, schema contracts, error handling.
- **`wrench-ux-constitution`** — UX governance — 11 design principles, Six Pillars
  initiative, Seven Empirical Gaps, and a pre-ship checklist.
- **`wrench-outreach`** — Multi-step B2B outreach sequences (email + LinkedIn),
  persona-specific, direct voice.
- **`meeting-dossier`** — Pre-meeting intelligence brief using live Wrench.ai lead
  score and enrichment data.
- **`dossier-builder`** — Contact intelligence dossier — Wrench.ai signals as a
  scannable HTML artifact.
- **`humanizer`** — Removes AI writing patterns — strips hedging, filler phrases,
  corporate-speak, and statistical-middle phrasing.
- **`seo-audit`** — Comprehensive SEO audit — technical, on-page, Core Web Vitals,
  crawl errors, indexing, and structured data.
- **`page-cro`** — Conversion rate optimization for marketing pages — above the fold,
  CTA clarity, social proof, and bounce rate analysis.
- Smithery registry listing (`smithery.yaml`)
- AEO-optimized `llms.txt`
- MIT `LICENSE`
